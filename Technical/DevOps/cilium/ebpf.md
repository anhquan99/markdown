# eBPF
## Definition
- Short for **extended Berkeley Packet Filter**.
- The initial feature is to filter packet, but it extended with many features and the packet filter feature becomes a small feature.
- It is a technology that allows you to run custom, sandboxed code directly inside the Linux Kernel without needing to change the kernel source code or load traditional, risky kernel modules.
- It is used to safely and efficiently extend the capabilities of the kernel without requiring to change kernel source code or load kernel modules.
- Unlocks kernel features for user-space applications by safely loading user-space apps to kernel feature without modules
	- Read data
	- Modify behavior
	- Unload or replace programs
![](/image/Pasted%20image%2020260607190151.png)
- Enhance capabilities:
	- Observability
	- Networking
	- Security
## Why?
- The OS kernel is hard to evolve due to its central role and high requirement towards stability and security. The rate of innovation at the operating system level has thus traditionally been lower compared to functionality implemented outside of the operating system.
- eBPF changes this formula fundamentally. It allows sandboxed programs to run within the operating system, which means that application developers can run eBPF programs to add additional capabilities to the operating system at runtime.
### Problem solves
- **Performance overhead in observability:** eBPF enables low-impact, event-driven monitoring.
- **Limited visibility into kernel and user-space interaction:** enables real-time tracing across kernel and user space.
- **Network packet processing latency:**
	- Faster packet processing than `iptables`
	- Ideal for high-performance, high-throughput networking
- **Runtime security and enforcement:** Detecting and stopping malicious behavior (suspicious syscalls, kernel rootkits, anomalous memory access) is hard with classic tools. eBPF enables dynamic policy enforcement and behavioral analysis in real time.
## How?
- eBPF programs are event-driven and are run when the kernel or an application passes a certain hook point.
![](/image/Pasted%20image%2020260607203150.png)
- Pre-defined hooks include system calls, function entry/exit, kernel tracepoints, network events, and several others.
![](/image/Pasted%20image%2020260607190447.png)
 - Example for some security tools, they use eBPF to block kernel calls.
![](/image/Pasted%20image%2020260607190635.png)
## XDP
- A eBPF program built for ultra-low latency networking tasks:
	- Dropping unwanted packets early
	- Redirect packets efficiently: load balancing, forwarding, ...
	- Modifying packets on the fly: NAT, tunneling, encapsulation, ...
### Replacing `iptables` with eBPF
- The `kube-proxy` use `iptables` to handle traffic, which get slower when the number of services increased.
- The `iptable` must be rebuilt all the rules when making changes.
![](/image/Pasted%20image%2020260607211649.png)
- The eBPF uses hash table to handle traffic, making it more scalable.
![](/image/Pasted%20image%2020260607211659.png)