# Application health check
- ArgoCD continuously monitors the health of deployed Kubernetes resources.
## Status
- Healthy
- Progressing
- Degraded: indicates a failure or an inability to reach a healthy state
- Missing: the resource is not present in the cluster
- Suspended: resource is suspended or paused
- Unknown: health assessment failed, and actual health status is unknown
## Checks
- Service and ingress are verified by the resource has been assigned IP or a hostname.
- Deployment, ReplicaSet, ... are verified by the replica count matches the desired count.
- PVC is checked by it is successfully bounded.
### Custom check
- ArgoCD support custom health checks written in Lua.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: moving-shapes-colors
data:
  CIRCLE_COLOR: "pink"
  OVAL_COLOR: "lightgreen"
  SQUARE_COLOR: "orange"
  TRIANGLE_COLOR: "white"  # using white will produce a Degraded message in Argo CD
  RECTANGLE_COLOR: "blue"
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  resource.customizations.health.ConfigMap: |
    hs = {}
    hs.status = "Healthy"
    hs.message = ""
    -- If the triangle color is white, mark as Degraded
    if obj.data ~= nil and obj.data.TRIANGLE_COLOR == "white" then
      hs.status = "Degraded"
      hs.message = "Use any color other than white for TRIANGLE_COLOR"
    end
    return hs

```