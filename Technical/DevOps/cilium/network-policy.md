# Network Policy
## Kubernetes network policy limitation
- K8s network policy works at layer 3 (IP) and layer 4 (TCP/UDP/SCTP).
- Can't match on DNS names or FQDNs.
- Limited pod attribute selectors: can't match on other attributes such as service account or richer metadata.
- Protocol matching constraints.
- No explicit deny rules.
- Only namespace scope.
- Multi-cluster awareness.
## Cilium network policy
- Use `endpointSelector` to select endpoint (pods, ...)
### L3
- Allow:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "rule-name"
  namespace: namespace
spec:
  endpointSelector:
    matchLabels:
      app: label
  ingress:
  - fromEndpoints:
    - matchLabels:
        app: ingress
  egress:
  - toEndpoints:
    - matchLabels:
        app: egress
```
- Allow all:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "allow-all"
  namespace: namespace
spec:
  endpointSelector:
    matchLabels:
      app: label
  ingress:
  - fromEndpoints:
    - {}
  egress:
  - toEndpoints:
    - {}
```
- Deny all:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "deny-all"
  namespace: namespace
spec:
  endpointSelector:
    matchLabels:
      app: lable
  # Empty lists deny all traffic
  ingress: []
  egress: []
```
- Match services:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "l3-rule-to-services"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: frontend
  egress:
  - toServices:
    # Services can be referenced by name + namespace
    - k8sService:
        serviceName: backend-service
        namespace: dev
    # Or by selector + namespace
    - k8sServiceSelector:
        selector:
          matchLabels:
            app: app1
        namespace: dev
```
- CIDR and CIDR sets:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "cidr-rule"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: frontend
  egress:
  - toCIDR:
    - 20.1.1.1/32
  - toCIDRSet:
    - cidr: 10.0.0.0/8
      except:
      - 10.96.0.0/12
```
### L4
- Allow TCP on port 80:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "l4-to-port-80"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: frontend
  egress:
  - toPorts:
    - ports:
      - port: "80"
        protocol: TCP
```
- Allow port range:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "l4-port-range"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: frontend
  egress:
  - toPorts:
    - ports:
      - port: "80"
        endPort: 444
        protocol: TCP
```
- Combine L3 and L4:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "l4-l3-combined"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: backend
  ingress:
  - fromEndpoints:
    - matchLabels:
        role: frontend
    toPorts:
    - ports:
      - port: "80"
        protocol: TCP
```
- Allow CIDR on specific port:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "egress-cidr-with-port"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: frontend
  egress:
  - toCIDR:
    - 192.0.2.0/24
    toPorts:
    - ports:
      - port: "80"
        protocol: TCP
```
### L7 (application layer)
- Allow GET /products request
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "http-method-path"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: backend
  ingress:
  - fromEndpoints:
    - matchLabels:
        role: frontend
    toPorts:
    - ports:
      - port: "80"
        protocol: TCP
      rules:
        http:
        - method: "GET"
          path: "/products"
```
- DNS filtering and FQDN:
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "dns-and-fqdn"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: backend
  egress:
  # Allow queries to kube-dns (namespace and label keys quoted to avoid YAML key ambiguity)
  - toEndpoints:
    - matchLabels:
        "k8s:io.kubernetes.pod.namespace": kube-system
        "k8s:k8s-app": kube-dns
    toPorts:
    - ports:
      - port: "53"
        protocol: ANY
      rules:
        dns:
        - matchName: "google.com"
        - matchPattern: "*.google.com"
  # Allow data egress to FQDNs resolved from those DNS responses
  - toFQDNs:
    - matchName: "google.com"
    - matchPattern: "*.google.com"
    toPorts:
    - ports:
      - port: "80"
        protocol: TCP
```
### Entities
- Cilium defines entities for common endpoint types such as the Kubernetes API server, host, and world (the public internet). Use `toEntities` or `fromEntities` to reference them.

```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "allow-kube-apiserver"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: backend
  egress:
  - toEntities:
    - kube-apiserver
```
### Localhost
- Default k8s allows node local processes to talk to pods on the same node. Cilium can change this behavior.
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "allow-host-to-backend"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: backend
  ingress:
  - fromEntities:
    - host
```
### NodePort and world entity
- When you expose a pod via a NodePort, the internet can reach it by default. If you attach a CiliumNetworkPolicy that denies ingress to that pod, the NodePort traffic will also be blocked unless you explicitly allow it. To permit ingress from the internet, allow the `world` entity.
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "allow-nodeport-world"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: backend
  ingress:
  - fromEntities:
    - world
```
### Deny policies
- Deny rules take precedence over allow rules, ensuring that disallowed sources remain blocked even if they match an allow rule.
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: "allow-frontend-with-deny"
  namespace: dev
spec:
  endpointSelector:
    matchLabels:
      role: backend
  ingress:
  - fromEndpoints:
    - matchLabels:
        role: frontend
  ingressDeny:
  - fromEndpoints:
    - matchLabels:
        test: test
```
### Cluster wide policies
```yaml
apiVersion: "cilium.io/v2"
kind: CiliumClusterwideNetworkPolicy
metadata:
  name: "clusterwide-backend-allow-frontends"
spec:
  endpointSelector:
    matchLabels:
      role: backend
  ingress:
  - fromEndpoints:
    - matchLabels:
        role: frontend
```
## Policy audit mode
- Policy audit mode lets you observe what a CiliumNetworkPolicy would do (allow or deny) without actually dropping traffic.
- This is useful for validating policies safely before enforcing them in production.
### Usage
- Validate policies by observing “would-be” denies without impacting live traffic.
- Identify false positives or unintended blocks before switching to enforcement.
- Roll out complex network policies gradually and safely.

|Policy Verdict|Meaning|
|---|---|
|none (INGRESS AUDITED)|Traffic would be denied by policy, but audit mode allowed the flow and recorded the audited verdict.|
|L3-Only (INGRESS ALLOWED)|Traffic is allowed by the policy (L3 match) and is permitted.|
|other verdicts|May indicate more specific L4/L7 evaluation — inspect Hubble logs for details.|
### Configuration
- Globally (all endpoints) via Helm — requires restarting Cilium components with config `policyAuditMode: true`.
- Per-endpoint (specific endpoints) using `cilium-dbg` — no cluster-wide restart required.
```bash
# Set these variables appropriately
CILIUM_NAMESPACE=kube-system
CILIUM_POD=<cilium-agent-pod-name>
ENDPOINT=<endpoint-id>

kubectl -n "$CILIUM_NAMESPACE" exec "$CILIUM_POD" -c cilium-agent -- \
 cilium-dbg endpoint config "$ENDPOINT" PolicyAuditMode=Enabled
  
# audit
kubectl -n kube-system exec cilium-pvq7s -- hubble observe flows -t policy-verdict --last 1
# Jun  3 06:57:16.456: default/frontend-5f44ddcfd6-lbvlz:35256 (ID:6443) -> default/db-584f4c666-wjfkq:80 (ID:6942) policy-verdict:none INGRESS AUDITED (TCP Flags: SYN)
```