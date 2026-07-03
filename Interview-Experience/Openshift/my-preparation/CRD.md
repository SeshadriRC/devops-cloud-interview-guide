A CRD extends Kubernetes by allowing you to create your own resource types.

Instead of only using built-in resources like:

- Pod
- Deployment
- Service

you can define custom resources.

Example:

kind: PostgreSQL

An operator watches this custom resource and automatically creates the required components, such as StatefulSets, Services, and PersistentVolumes.

In your project, operators use CRDs to provision services like PostgreSQL, Redis, or AWS resources.
