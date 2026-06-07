# eBPF
## Definition
- Short for **extended Berkeley Packet Filter**.
- It is a technology that allows you to run custom, sandboxed code directly inside the Linux Kernel without needing to change the kernel source code or load traditional, risky kernel modules.
- It is used to safely and efficiently extend the capabilities of the kernel without requiring to change kernel source code or load kernel modules.
![](/image/Pasted%20image%2020260607190151.png)
## Why?
- The OS kernel is hard to evolve due to its central role and high requirement towards stability and security. The rate of innovation at the operating system level has thus traditionally been lower compared to functionality implemented outside of the operating system.
- eBPF changes this formula fundamentally. It allows sandboxed programs to run within the operating system, which means that application developers can run eBPF programs to add additional capabilities to the operating system at runtime.
## How?
- eBPF programs are event-driven and are run when the kernel or an application passes a certain hook point.
- Pre-defined hooks include system calls, function entry/exit, kernel tracepoints, network events, and several others.
![](/image/Pasted%20image%2020260607190447.png)
 - Example for some security tools, they use eBPF to block kernel calls.
![](/image/Pasted%20image%2020260607190635.png)