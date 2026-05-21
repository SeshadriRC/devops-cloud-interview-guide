What you described is a pretty common chaos engineering setup in enterprise OpenShift/Kubernetes environments.

The important thing is:

* Jenkins is running externally (management cluster)
* Jenkins connects remotely to another OpenShift cluster
* Pipeline authenticates using cluster API + token
* It identifies worker nodes
* Then triggers node reboot one by one (or in batches)

High-level flow looks like this:

```text
Jenkins Pipeline
      ↓
Login to target OpenShift cluster
      ↓
Get worker node list
      ↓
Drain node
      ↓
SSH / SSM / cloud API reboot
      ↓
Wait for node Ready
      ↓
Proceed to next worker
```

---

# How They Usually Achieve This

There are mainly 3 methods.

## Method 1 — SSH Reboot (Most Common)

Pipeline does:

```bash
oc login https://api.aws-dev-1.company.com --token=xxxx

oc get nodes
```

Filter worker nodes:

```bash
oc get nodes -l node-role.kubernetes.io/worker
```

Then:

```bash
oc adm drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

Then Jenkins SSHs into node:

```bash
ssh core@worker-node "sudo reboot"
```

After reboot:

```bash
oc adm uncordon <node-name>
```

---

# Why Drain?

Drain safely evicts workloads before reboot.

Without drain:

* pods abruptly die
* applications may fail
* data loss possible

Drain means:

* Kubernetes reschedules pods elsewhere first

---

# How Jenkins Connects to Another Cluster

Usually using:

## OpenShift Token

You mentioned they ask for:

* cluster name
* OpenShift token

That token is likely:

```bash
oc login --token=<token>
```

Jenkins pipeline internally executes:

```bash
oc login api-url --token=xxx
```

using either:

* Jenkins credential store
* parameterized pipeline
* secret text credential

---

# How They Identify Only Worker Nodes

OpenShift nodes have labels.

Example:

```bash
oc get nodes --show-labels
```

Workers usually have:

```text
node-role.kubernetes.io/worker=
```

Masters/control plane:

```text
node-role.kubernetes.io/master=
```

So pipeline probably runs:

```bash
oc get nodes -l node-role.kubernetes.io/worker
```

or:

```bash
oc get nodes | grep worker
```

---

# But How Does Jenkins Actually Reboot VM?

This is the important part.

Jenkins itself cannot reboot Kubernetes node directly.

It needs infrastructure access.

Usually one of these:

---

# Option A — SSH

Most common in on-prem and OpenShift VM environments.

Pipeline:

```bash
ssh core@node sudo reboot
```

Works if:

* Jenkins has SSH key
* firewall open
* node reachable

---

# Option B — AWS CLI / EC2 API

Since your cluster name looks AWS-based (`aws-dev-1`), very possible.

Pipeline may use:

```bash
aws ec2 reboot-instances --instance-ids i-12345
```

How?

First map node → EC2 instance:

```bash
oc get node -o wide
```

or:

```bash
aws ec2 describe-instances
```

This is cleaner than SSH.

Very common in managed cloud environments.

---

# Option C — SSM Run Command

Enterprise-friendly AWS method.

Instead of SSH:

```bash
aws ssm send-command
```

Example:

```bash
aws ssm send-command \
  --document-name "AWS-RunShellScript" \
  --parameters commands=["sudo reboot"]
```

Advantages:

* no SSH needed
* audited
* secure

Large enterprises prefer this.

---

# Typical Jenkins Pipeline Logic

A simplified version:

pipeline {

    agent any

    parameters {

        string(
            name: 'CLUSTER_API',
            defaultValue: 'https://api.cluster.example.com:6443',
            description: 'OpenShift Cluster API URL'
        )

        password(
            name: 'OCP_TOKEN',
            defaultValue: '',
            description: 'OpenShift Login Token'
        )

        string(
            name: 'REBOOT_WAIT',
            defaultValue: '120',
            description: 'Wait time after reboot in seconds'
        )
    }

    stages {

        stage('Login To OpenShift Cluster') {
            steps {

                echo "Logging into OpenShift cluster..."

                sh """
                oc login ${params.CLUSTER_API} \
                --token=${params.OCP_TOKEN} \
                --insecure-skip-tls-verify=true
                """
            }
        }

        stage('Fetch Worker Nodes') {
            steps {

                script {

                    echo "Fetching worker nodes..."

                    WORKER_NODES = sh(
                        script: """
                        oc get nodes \
                        -l node-role.kubernetes.io/worker \
                        --no-headers | awk '{print \$1}'
                        """,
                        returnStdout: true
                    ).trim().split("\\n")

                    echo "Worker nodes identified:"

                    WORKER_NODES.each {
                        echo it
                    }
                }
            }
        }

        stage('Chaos Reboot Worker Nodes') {
            steps {

                script {

                    for (node in WORKER_NODES) {

                        echo "======================================"
                        echo "Starting reboot activity for ${node}"
                        echo "======================================"

                        // Drain node safely
                        sh """
                        oc adm drain ${node} \
                        --ignore-daemonsets \
                        --delete-emptydir-data \
                        --force
                        """

                        // Reboot worker node through SSH
                        sh """
                        ssh -o StrictHostKeyChecking=no \
                        core@${node} \
                        sudo reboot || true
                        """

                        // Wait for reboot
                        sh """
                        echo "Waiting ${params.REBOOT_WAIT} seconds for reboot..."
                        sleep ${params.REBOOT_WAIT}
                        """

                        // Wait until node becomes Ready
                        sh """
                        oc wait \
                        --for=condition=Ready node/${node} \
                        --timeout=300s
                        """

                        // Enable scheduling again
                        sh """
                        oc adm uncordon ${node}
                        """

                        // Validate node status
                        sh """
                        oc get nodes
                        """

                        echo "Completed reboot activity for ${node}"
                    }
                }
            }
        }

        stage('Post Validation') {
            steps {

                echo "Performing cluster validation..."

                sh """
                oc get nodes
                oc get pods -A
                oc get co
                """
            }
        }
    }

    post {

        success {

            echo "Chaos testing completed successfully."
        }

        failure {

            echo "Chaos testing failed."
        }

        always {

            echo "Pipeline execution finished."
        }
    }
}

---

# Very Important Production Safety Checks

Real production pipelines also:

## Wait for node Ready again

```bash
oc wait --for=condition=Ready node/<node>
```


## Reboot one node at a time

To avoid cluster outage.


## Skip infra/control nodes

Only worker nodes rebooted.


## Check workload health

Example:

```bash
oc get co
oc get pods -A
```


# Why Chaos Testing Does This

Purpose:

* validate self-healing
* verify pod rescheduling
* test HA
* test autoscaling
* validate stateful workloads
* ensure applications survive node failures

This is basically:

* node failure simulation
* resilience testing

A core SRE/DevOps practice.


# In Your Environment Most Likely Architecture

Very likely:

```text
Dedicated Jenkins Cluster
        ↓
oc login to target cluster
        ↓
Get worker nodes
        ↓
Drain node
        ↓
AWS reboot OR SSH reboot
        ↓
Wait for Ready
        ↓
Uncordon
```

Because you said:

* Jenkins on separate cluster
* multiple OpenShift clusters
* token-based access
* AWS naming

I strongly suspect:

* `oc login`
* `oc adm drain`
* AWS EC2 reboot or SSH reboot

are being used internally.

---
