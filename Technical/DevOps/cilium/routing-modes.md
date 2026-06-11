# Routing Modes
## Problem
- The request flow from source node to destination node is through source router to the destination route. The k8s CIDR model assign the pod with internal IP, so the request is dropped when it is getting to the router because the router can not find the path the IP.
![](/image/Pasted%20image%2020260608145205.png)
## Encapsulation mode (default)
- The solution is when the packet is routing, it is encapsulated with another network packet with the source IP of the source router and then destination IP of the destination router.
![](/image/Pasted%20image%2020260608145505.png)
### Headers
```
Outer packet: Source IP router - Destination IP router
Encapsulate header (example: VXLAN header)
Source IP pod - Destination IP pod
Payload
```
### Requirements
- Node-to-node IP connectivity: every node IP must be reachable from every other node.
- Firewall and security groups must allow the tunnel/encapsulation protocol (UDP ports used by VXLAN/Geneve, etc.).
- Ensure MTU (Maximum Transmission Unit) is sized to account for tunnel overhead (or enable jumbo frames - Ethernet packets designed to carry more data than the standard limit).
### Protocol options
| Protocol                        | Typical UDP Port                             | Notes                                                                        |
| ------------------------------- | -------------------------------------------- | ---------------------------------------------------------------------------- |
| VXLAN (Virtual Extensible LAN)) | 4789 (historically 8472 in some deployments) | Widely supported; CNI implementations often default to VXLAN                 |
| Geneve                          | 6081                                         | Flexible, extensible header options; used by some CNIs for advanced features |
### Overhead
- Encapsulation uses extra 50 bytes for encapsulation leading to reduce effective MTU by 50.
![](/image/Pasted%20image%2020260608150525.png)

| Pros                                                  | Cons                        |
| ----------------------------------------------------- | --------------------------- |
| Simplifies network configuration                      | Increase overhead           |
| No requirement for underlay network changes           | Higher latency              |
| Work across different network topologies              | More complex debugging      |
| Less dependency on cloud or network provider features | Potential for fragmentation |
## Native routing mode
- The general solution is to get the router to know where to route without encapsulation.
### Options
- Static routes — simple but not scalable or fault-tolerant.
- Dynamic routing (BGP) — Cilium can advertise pod CIDRs into the physical fabric using BGP, so routers learn where to forward pod traffic.
- SDN(Software-Defined Networking)/orchestration solutions that distribute routes to the underlay: the network management software (SDN) is automatically configuring the physical hardware (underlay) to ensure that traffic knows exactly how to travel between virtual workloads (like your Kubernetes Pods or VMs).
![](/image/Pasted%20image%2020260608153142.png)
### Configuration
```yaml
# cilium-config snippet (example)
routingMode: "native"

# IPv4 CIDR(s) that will use native routing (e.g., your cluster Pod CIDR)
ipv4NativeRoutingCIDR: "{ipv4-range}"
ipv6NativeRoutingCIDR: "{ipv6-range}"
```
- Cilium allows dynamic change routing mode based on the CIDRs. When it is out of the defined CIDR os the mode it is changed to default mode which is encapsulation.

| Pros                                 | Cons                          |
| ------------------------------------ | ----------------------------- |
| Better performance                   | Requires network support      |
| More efficient use of MTU            | Complex routing configuration |
| Easier debugging                     | Not always cloud friendly     |
| Optimized for large-scale deployment |                               |
## VXLAN
- **VXLAN (Virtual Extensible LAN)** is a networking technology that allows you to create "virtual" networks on top of your existing physical network. It essentially takes your standard Layer 2 Ethernet frames and wraps them inside Layer 3 UDP packets.
### How it works?
1. **Inner Packet:** Your original data (the Ethernet frame from a Pod or VM) is kept exactly as it is.
2. **VXLAN Header:** A small label is added to it (called the **VNI** or VXLAN Network Identifier). The VNI is 24 bits long, meaning you can have over **16 million virtual networks** (compared to the 4,000 in standard VLANs).
3. **UDP Wrapper:** The entire thing is then wrapped in a standard UDP packet.
4. **Outer Header:** It gets an IP header that points to the destination server (the **VTEP** - VXLAN Tunnel End Point).