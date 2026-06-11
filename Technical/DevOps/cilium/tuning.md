# Tuning
## Netkit
- Each Kubernetes node has a host network namespace (with the main interface to the outside world).
- Each Pod runs in its own network namespace.
- Pods and the host are typically connected via a **veth** (Virtual Ethernet) pair; many of the tunables below replace or bypass parts of that path to reduce host-side processing and latency.
- Netdev datapath (replace veth) Cilium can replace the veth pair between host and pod with a kernel/AF_XDP-based datapath (netdev). This reduces host-side overhead and gives better throughput and lower latency compared to the traditional veth path because fewer context switches and less software processing are involved.
![](/image/Pasted%20image%2020260611184149.png)
### Configuration
```bash
helm install cilium cilium/cilium --version 1.17.4 \
  --namespace kube-system \
  --set routingMode=native \
  --set bpf.datapathMode=netdev \
  --set bpf.masquerade=true \
  --set kubeProxyReplacement=true
```
## Host routing
- Host routing (bypass the host netfilter/iptables path).
- By default, pod traffic may traverse the host network stack and be processed by netfilter/iptables.
- Host routing lets Cilium bypass parts of the host stack for selectable flows, which reduces overhead and improves forwarding efficiency.
### Configuration
```bash
helm install cilium cilium/cilium --version 1.17.4 \
  --namespace kube-system \
  --set bpf.masquerade=true \
  --set kubeProxyReplacement=true
```
## BIG TCP
- Cilium supports enabling BIG TCP to allow much larger aggregated TCP segments (for example, up to ~1 MiB), which can reduce pps from millions to tens of thousands for equivalent throughput.
```ad-note
BIG TCP requires kernel and NIC driver support. Test carefully on staging before enabling in production. Incorrect assumptions about offloads or driver behavior can cause performance issues or packet corruption.
```
![](/image/Pasted%20image%2020260611184634.png)
### Configuration
```bash
helm install cilium cilium/cilium --version 1.17.4 \
  --namespace kube-system \
  --set routingMode=native \
  --set bpf.masquerade=true \
  --set ipv6.enabled=true \
  --set enableIPv4BIGTCP=true \
  --set enableIPv6BIGTCP=true \
  --set kubeProxyReplacement=true
```