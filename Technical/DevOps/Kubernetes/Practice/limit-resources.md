# Limit resources
## Specification
### CPU
- 100m = 0.1 CPU
- Minimum value is 1m
- 1 CPU =
	- 1 AWS vCPU
	- 1 GCP Core
	- 1 Azure Core
	- 1 Hyperthread
### Memory
- 1G (Gigabyte) = 1,000,000,000 bytes
- 1M (Megabyte) = 1,000,000 bytes
- 1K (Kilobyte) = 1,000 bytes
- 1Gi (Gibibyte) = 1,073,741,824 bytes
- 1Mi (Mebibyte) = 1,048,576 bytes
- 1Ki (Kibibyte) = 1,024 bytes
### Requests
- Guarantee a request specifies the minimum amount of a resource (CPU or memory) that a container is guaranteed to receive.
### Limits
- Restriction a limit specifies the maximum amount of a resource that a container is allowed to consume.
## Deployment
- You can configure the resource limit in:
	- `spec.containers[].resources.limits.cpu`
	- `spec.containers[].resources.limits.memory`
	- `spec.containers[].resources.limits.hugepages-<size>`
	- `spec.containers[].resources.requests.cpu`
	- `spec.containers[].resources.requests.memory`
	- `spec.containers[].resources.requests.hugepages-<size>`
## Namespace
### `LimitRange`
- In namespace, it uses a resource with kind `LimitRange` to set limit at a pod and container level on the namespace.
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
### `ResourceQuota`
- `ResourceQuota` is for limiting the total resource consumption for a namespace.
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
spec:
  hard:
    requests.cpu: "1"
    requests.memory: "1Gi"
    limits.cpu: "2"
    limits.memory: "2Gi"
    requests.nvidia.com/gpu: 4
```