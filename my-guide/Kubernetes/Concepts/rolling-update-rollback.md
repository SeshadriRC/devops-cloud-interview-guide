### Rollback in Rolling Update Strategy – Summary

In a **Rolling Update** deployment strategy, Kubernetes gradually replaces old pods with new pods.

If the new version has issues, rollback means:

> Reverting the Deployment back to the previous ReplicaSet/version.

---

## How Kubernetes Supports Rollback

Every Deployment maintains:

* ReplicaSet history
* Previous application versions

So rollback becomes easy.

---

## Rollback Command

To rollback to the previous version:

```bash id="hgvysb"
kubectl rollout undo deployment <deployment-name>
```

Example:

```bash id="3nk4n0"
kubectl rollout undo deployment myapp
```

---

## What Happens Internally

Kubernetes:

1. Finds previous ReplicaSet
2. Scales down current bad version
3. Scales up old stable version

This happens gradually using Rolling Update settings.

---

## Check Rollout History

To see previous versions:

```bash id="mjlwm7"
kubectl rollout history deployment <deployment-name>
```

Example:

```bash id="a3ywqz"
kubectl rollout history deployment myapp
```

---

## Rollback to Specific Revision

```bash id="ah5v8t"
kubectl rollout undo deployment myapp --to-revision=2
```

---

## Example Rolling Update Config

```yaml id="0lf4q8"
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 1
```

During rollback:

* Kubernetes follows same rolling mechanism
* Old stable pods are gradually restored
* Downtime is minimized

---

## Easy Interview Answer

> “In Rolling Update strategy, rollback is performed using `kubectl rollout undo deployment`. Kubernetes uses the previous ReplicaSet history to restore the earlier stable version gradually, following the same rolling update process with minimal downtime.”
