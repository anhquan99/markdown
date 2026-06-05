# Cleanup Policy
## Definition
- Kyverno has the ability to cleanup (i.e., delete) existing resources in a cluster in two different ways.
- The first way is via a declarative policy definition in either a `CleanupPolicy` or `ClusterCleanupPolicy`.
## Structure
```yaml
apiVersion: kyverno.io/v2
kind: ClusterCleanupPolicy
metadata:
  name: cleandeploy
spec:
  # which resources to target?
  match:
    any:
      - resources:
          kinds:
            - Deployment
          selector:
            matchLabels:
              canremove: 'true'
  # under what extra. condition? (optional)
  conditions:
    any:
      - key: '{{ target.spec.replicas }}'
        operator: LessThan
        value: 2
  # when to run the cleanup? (required)
  # standard cron syntax
  schedule: '*/5 * * * *'
```
- Require cluster role which allow Kyverno to cleanup resources. The same as RBAC aggregation.
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    app.kubernetes.io/component: cleanup-controller
    app.kubernetes.io/instance: kyverno
    app.kubernetes.io/part-of: kyverno
  name: kyverno:cleanup-pods
rules:
  - apiGroups:
      - ''
    resources:
      - pods
    verbs:
      - get
      - watch
      - list
      - delete
```
## Cleanup label
- Label a resource that will schedule the delete time of that resource.
- Use the label `cleanup.kyverno.io/ttl`.
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    cleanup.kyverno.io/ttl: 2m
  name: foo
spec:
  containers:
    - args:
        - sleep
        - 1d
      image: busybox:1.35
      name: foo
```
- Time format:
	- Relative duration
	- Absolute timestamp (ISO 8601)
- The default time of `ttlReconciliationInterval` is 1m.