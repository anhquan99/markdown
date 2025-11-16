# Network Policies
## Definition
- Kubernetes was designed to allow all Pods to communicate freely, without restrictions, with all other Pods in cluster Namespaces. In time, it became clear that it was not an ideal design, and mechanisms needed to be put in place in order to restrict communication between certain Pods and applications in the cluster Namespace. [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) are sets of rules which define how Pods are allowed to talk to other Pods and resources inside and outside the cluster. Pods not covered by any Network Policy will continue to receive unrestricted traffic from any endpoint. 
- Network Policies are very similar to typical Firewalls. They are designed to protect mostly assets located inside the Firewall but can restrict outgoing traffic as well based on sets of rules and policies. 
## Prerequisites
- Network policies are implemented by the network plugin.
## Implementation
- The Network Policy API resource specifies `podSelectors`, **Ingress** and/or **Egress** `policyTypes`, and rules based on source and destination `ipBlocks` and **ports**. Very simplistic default allow or default deny policies can be defined as well. As a good practice, it is recommended to define a default deny policy to block all traffic to and from the Namespace, and then define sets of rules for specific traffic to be allowed in and out of the Namespace. 
- By default, Network Policies are namespaced API resources, but certain network plugins provide additional features so that Network Policies can be applied cluster-wide.
- It is implemented by the Network Plugins CNI (Calico /Weave).
### Without NetworkPolicies
- By default every pod can access every pod.
- Pods **are** not isolated.
```ad-note
If there is no network policy rule, it will not allow any network in or out of pod depends on the `policyTypes`
```
## `policyTypes`
### Egress
- If there is any rule(s) defined, it will allow outgoing network traffic.
- Condition inside the egress block:
	- Multiple `to` block is evaluated with `OR` operator
	- Inside the `to` block is evaluated with `AND` operator
```yaml
...
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```
### Ingress
- If there is any rule(s) defined, it will allow ingoing network traffic
### Behavior of `to` and `from` selectors
- `podSelector`: selects particular Pods in the same namespace as the NetworkPolicy
- `namespaceSelector`: selects particular namespaces
- `namespaceSelector` and podSelector`: 
	- If these 2 defined in a single entry, it selects particular Pods within particular namespaces
	- If these 2 defined in a separated entry, it uses `OR` operator to select the Pods
```yaml
# single entry
# select the pods with lable role=client and in namespace with lable user=alice
...
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          user: alice
      podSelector:
        matchLabels:
          role: client
...

# separate entry
# select the pods in namespace with lable user=alice or pods with role=client
...
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          user: alice
    - podSelector:
        matchLabels:
          role: client
...
```
- `ipBlock`
- `ports`
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space1
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space2
	ports: # AND operator
	    - port: 53
	      protocol: TCP
	    - port: 53
	      protocol: UDP
  - ports: # OR operator
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
```
## Multiple NetworkPolicies
- Network policies do not conflict, they are additive. It means if there are multiple network polices, those policies will be union and the order of evaluation does not affect the policy result.
## Default policies
- By default, if no policies exist in a namespace then all ingress and egress traffic is allowed to and from pods in that namespace.
### Default deny all traffic
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```
### Allow all traffic
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-ingress
spec:
  podSelector: {}
  ingress:
  - {}
  egress:
  - {}
  policyTypes:
  - Ingress
  - Egress

```
## References
- https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors