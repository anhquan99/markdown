# Limit resources
## Deployment
- You can configure the resource limit in:
	- `spec.containers[].resources.limits.cpu`
	- `spec.containers[].resources.limits.memory`
	- `spec.containers[].resources.limits.hugepages-<size>`
	- `spec.containers[].resources.requests.cpu`
	- `spec.containers[].resources.requests.memory`
	- `spec.containers[].resources.requests.hugepages-<size>`
## Namespace
- In namespace, it uses a resource with kind `LimitRange` to set limit on the namespace.
- Example:
```yaml
apiVersion: v1
kind: LimitRange
metadata:
	name: resource_name
spec:
	limits:
	- default:
		cpu: 1
		memory: 500Mi
	defaultRequest:
		cpu: 0.5
		memory: 100Mi
	type: Container
```