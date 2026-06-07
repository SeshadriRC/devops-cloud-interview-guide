**Add the helm repo**

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
"prometheus-community" has been added to your repositories

helm repo add <repo-name-anything> https://prometheus-community.github.io/helm-charts
```
---
**list helm repo**

```bash
helm repo list

root@controlplane:~$ helm repo list 
NAME                    URL                                               
prometheus-community    https://prometheus-community.github.io/helm-charts
prometheus-community2   https://prometheus-community.github.io/helm-charts
```
---

**Search in helm repo**

```bash
# Below will list all the charts from both the repo
helm search repo 

# Below will list only the mentioned chart
root@controlplane:~$ helm search repo kube-prometheus-stack
NAME                                            CHART VERSION   APP VERSION     DESCRIPTION                                       
prometheus-community/kube-prometheus-stack      86.2.0          v0.91.0         kube-prometheus-stack collects Kubernetes manif...
prometheus-community2/kube-prometheus-stack     86.2.0          v0.91.0         kube-prometheus-stack collects Kubernetes manif...
```
---

**List the helm release in particular namespace**

```bash
root@controlplane:~/.kube$ helm ls -n monitoring
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
prometheus      monitoring      1               2026-06-07 01:55:20.963064638 +0000 UTC deployed        kube-prometheus-stack-86.2.0    v0.91.0    
```

---

**Uninstall the helm release**

```bash
root@controlplane:~/.kube$ helm uninstall prometheus -n monitoring
release "prometheus" uninstalled
```

---
