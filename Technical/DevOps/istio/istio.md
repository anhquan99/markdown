# Istio
## Definition
- An open source service mesh that layers transparently onto existing distributed applications.
## Modes
- **Sidecar**: deploys an Envoy proxy along with each pod that you start in your cluster, or running alongside services running on VMs.
- **Ambient**: uses a per-node Layer 4 proxy, and optionally a per-namespace Envoy proxy for Layer 7 features.
## Components
### Sidecars
- The Sidecar describes the configuration of the sidecar proxy that mediates inbound and outbound communication to the workload instance it is attached to.
```yaml
apiVersion: networking.istio.io/v1
kind: Sidecar
metadata:
  name: default
  namespace: test
spec:
  egress:
  - hosts:
    - "./*"
    - "istio-system/*"
```
#### PeerAuthentication
```yaml
apiVersion: security.istio.io/v1
kind: PeerAuthentication
metadata:
  name: default
  namespace: default
spec:
  mtls:
    mode: STRICT
```
### Virtual services
- Defines a set of traffic routing rules to apply when a host is addressed.
- Each routing rule defines matching criteria for traffic of a specific protocol.
- If the traffic is matched, then it is sent to a named destination service (or subset/version of it) defined in the registry.
```yaml
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: httpbin
  namespace: default
spec:
  hosts:
  - httpbin
  http:
  - match: 
    - uri:
        prefix: /hello
    rewrite:
      uri: /
    route:
    - destination:
        host: httpbin.default.svc.cluster.local
        port:
          number: 8000
  - match:
    - uri:
        prefix: /
    route:
    - destination:
        host: httpbin.default.svc.cluster.local
        port:
          number: 8000
```
### Destination rules
- Defines policies that apply to traffic intended for a service after routing has occurred.
- These rules specify configuration for load balancing, connection pool size from the sidecar, and outlier detection settings to detect and evict unhealthy hosts from the load balancing pool.
```yaml
apiVersion: networking.istio.io/v1
kind: DestinationRule
metadata:
  name: bookinfo-ratings
spec:
  host: ratings.prod.svc.cluster.local
  trafficPolicy:
    loadBalancer:
      simple: LEAST_REQUEST
```
### Gateways
- Describes a load balancer operating at the edge of the mesh receiving incoming or outgoing HTTP/TCP connections.
```yaml
apiVersion: networking.istio.io/v1
kind: Gateway
metadata:
  name: istio-egressgateway
spec:
  selector:
    istio: egressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - www.wikipedia.org
---
apiVersion: networking.istio.io/v1
kind: ServiceEntry
metadata:
  name: wikipedia
spec:
  hosts:
  - www.wikipedia.org
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  - number: 443
    name: https
    protocol: HTTPS
  resolution: DNS
---
apiVersion: networking.istio.io/v1
kind: DestinationRule
metadata:
  name: egressgateway-for-wikipedia
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
  - name: wikipedia
---
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: wikipedia-egress-gateway
spec:
  hosts:
  - www.wikipedia.org
  gateways:
  - istio-egressgateway
  - mesh
  http:
  - match:
    - gateways:
      - mesh
      port: 80
    route:
    - destination:
        host: istio-egressgateway.istio-system.svc.cluster.local
        subset: wikipedia
        port:
          number: 80
      weight: 100
  - match:
    - gateways:
      - istio-egressgateway
      port: 80
    route:
    - destination:
```
### Service entries

### Service breakers

### Service entry vs workload entry

|                       | Service entry                                      | Workload entry                                                               |
| --------------------- | -------------------------------------------------- | ---------------------------------------------------------------------------- |
| Purpose               | Defines an external service for traffic routing    | Defines an external workload inside the mesh (like a pod in the k8s cluster) |
| Use case              | For exposing external APIs or services to the mesh | For integrating external workload into the mesh                              |
| mTLS support          | Does not directly involve mTLS                     | Support mTLS and workload identity                                           |
| Service discovery     | Provides service discovery for external services   | Used for redirecting traffic to specified external workloads                 |
| Telemetry and metrics | Limited                                            | Provides logs, metrics for external workloads                                |
