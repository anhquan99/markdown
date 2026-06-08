# Configuration
## Installation
### Components
- Agent: DaemonSet - cilium
- Envoy: DaemonSet - cilium-envoy
- Operator: Deployment - cilium-operator
- Configuration: ConfigMap - cilium-config
- Cluster role and binding for:
	- Agent: cilium
	- Operator: cilium-operator
	- Envoy: cilium-envoy
### Methods
- Cilium CLI
```ad-note
There is another Cilium CLI used in agent pod, used for advanced debugging
```
- Helm
## IP address management (IPAM)
### K8s pod IP addressing
- K8s assign IP to the pod with the pod CIDR.
- Each node will get a slide of the pod CIDR.
- Example: the pod CIDR is 10.244.0.0/16 then the 1st node get a slide of IP which is 10.244.1.0/24, 2nd node is 10.244.2.0/24 and the 3rd node is 10.244.3.0/24.
![](/image/Pasted%20image%2020260608100040.png)
### Modes
```ad-danger
Changin IPAM mode in a live environement may cause persistent disruption of connectivity for existing workloads. To change it, you should deploy a new cluster with desired mode and migrate workload to the new cluster.
```
#### K8s host scope
- In this mode, the k8s control plane will have to handle the allocation of the node-specific CIDR range. Example the 10.244.1.0/24 in the last example.
- Configuration in control plane:
	- `--allocate-node-cidrs=true`
	- `--cluster-cidr={ipv4,ipv6}`
	- `--node-cidr-mask-size={mask-size}`
- To use this mode, set these configuration in Cilium configuration:
	- `ipam.mode: "kubernetest"`
	- `k8s.requireIPv4PodCIDR: true`
	- `k8s.requireIPv6PodCIDR: true`
- Cilium have to figure out what range is assigned to a specific node. It will look into the Node object in the `podCIDRs` spec.
![](/image/Pasted%20image%2020260608100759.png)
#### Cluster scope (default)
- In this mode, Cilium operator will be responsible for assigning the node-specific CIDR range.
- To use this mode, set these configuration in Cilium configuration:
	- `ipam.mode: "cluster-pool"`
	- `ipam.operator.clusterPoolIPv4PodCIDRList: [{cidr-ipv4-list}]`
	- `ipam.operator.clusterPoolIPv4MaskSize: {mask-size}`
	- `ipam.operator.clusterPoolIPv6PodCIDRList: [{cidr-ipv6-list}]`
	- `ipam.operator.clusterPoolIPv6MaskSize: {mask-size}`
- The `CiliumNode` CRD is used to store the information about the IP.
![](/image/Pasted%20image%2020260608101528.png)
- To get node CIDR use: `kubectl exec -n kube-system cilium-cfkzf -- cilium-dbg status --all-addresses`
#### Other modes
- Cloud-Specific
- Multi-Pool
- CRD-Backed

| Feature                    | Kubernetes Host Scope | Cluster Scope (default) | Multi-Pool | CRD-backed | AWS ENI        | Azure IPAM       | GKE            |
| -------------------------- | --------------------- | ----------------------- | ---------- | ---------- | -------------- | ---------------- | -------------- |
| Tunnel routing             | ✅                     | ✅                       | ✅          | ❌          | ❌              | ❌                | ❌              |
| Direct routing             | ✅                     | ✅                       | ✅          | ✅          | ✅              | ✅                | ✅              |
| CIDR Configuration         | Kubernetes            | Cilium                  | Cilium     | External   | External (AWS) | External (Azure) | External (GCP) |
| Multiple CIDRs per cluster | ❌                     | ✅                       | ✅          | N/A        | N/A            | N/A              | N/A            |
| Multiple CIDRs per node    | ❌                     | ❌                       | ✅          | N/A        | N/A            | N/A              | N/A            |
| Dynamic CIDR/IP allocation | ❌                     | ❌                       | ✅          |            |                |                  |                |
## Replace `kube-proxy`
- Check configuration: `kubeProxyReplacement`.
```bash
kubectl -n kube-system exec ds/cilium -- cilium-dbg status | grep KubeProxyReplacement
```
### Replace steps
1. Remove `kube-proxy` artifacts: DaemonSet, ConfigMap. Remove ConfigMap prevents `kubeadm` from automatically re-installing `kube-proxy` during control-plan upgrades.
```bash
kubectl -n kube-system delete ds kube-proxy
# Delete the ConfigMap as well to avoid kube-proxy being reinstalled during a kubeadm upgrade
kubectl -n kube-system delete cm kube-proxy
```
2. Remove all the k8s networking logic from node
```bash
iptables-save | grep -v KUBE | iptables-restore
```
3. Set Cilium config
```yaml
kubeProxyReplacement: "true"
k8sServiceHost: "host-ip-control-plane"
k8sServicePort: "6443"
```
4. Verify
```bash
kubectl -n kube-system exec ds/cilium -- cilium-dbg status | grep KubeProxyReplacement
kubectl -n kube-system exec ds/cilium -- cilium-dbg status --verbose | grep KubeProxyReplacement
```
