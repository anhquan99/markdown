# Network

|Networking Tools|Description|
|---|---|
|`ethtool`|Queries network interfaces and can also set various parameters such as the speed|
|`netstat`|Displays all active connections and routing tables; useful for monitoring performance and troubleshooting|
|`nmap`|Scans open ports on a network; important for security analysis|
|`tcpdump`|Dumps network traffic for analysis|
|`iptraf`|Monitors network traffic in text mode|
|`mtr`|Combines functionality of ping and traceroute and gives a continuously updated display|
|`dig`|Tests DNS workings; a good replacement for host and nslookup|
## Network config files
- Network configuration files are essential to ensure that interfaces function correctly.
- They are located in the `/etc` directory tree.
## `route` or `ip route`
- Used to view or change the IP routing table to add, delete, or modify specific (static) routes to specific hosts or networks.
## `traceroute`
- Used to inspect the route which the data package takes to reach the destination host, which makes it quite useful for troubleshooting network delays and errors.
## `wget`
- Used to download files with:
	- Large file downloads
	- Recursive downloads, where a web page refers to other web pages and all are downloaded at once
	- Password-required downloads
	- Multiple file downloads.
## `curl`
- Used to obtain information about a URL.
## Predictable Network Interface Device Names (PNIDN)
- PNIDN is connected to `udev` and integrated with `systemd`.
### Types
1. Incorporating Firmware or BIOS provided index numbers for onboard devices  
    Example: `eno1`
2. Incorporating Firmware or BIOS provided PCI Express hotplug slot index numbers  
    Example: `ens1`
3. Incorporating physical and/or geographical location of the hardware connection  
    Example: `enp2s0`
4. Incorporating the MAC address  
    Example: `enx7837d1ea46da`
5. Using the old classic method  
    Example: `eth0`
## Network manager
- `nmcli`: network manager command line interface.
- `nmtui`: network manager text user interface.
## Routing
- Routing is the process of selecting paths in a network along which to send network traffic. The routing table is a list of routes to other networks managed by the system. It defines paths to all networks and hosts; local packets are sent directly, while remote traffic is sent to routers.
- Either use `route` (deprecated) or `ip` command to see current routing table.
### Default route
- The default route is the way packets are sent when there is no other match in the routing table for reaching the specified network.
### Static route
- Static routes are used to control packet flow when there is more than one router or route. They are defined for each interface and can be either persistent or non-persistent.
- When the system can access more than one router, or perhaps there are multiple interfaces, it is useful to selectively control which packets go to which router.
## Teaming and bonding network interfaces
- Teaming and Bonding interfaces allow dynamic network interface configuration to provide higher throughput and additional robustness to the network.
- **Bonding**: uses a kernel module to provide aggregation of network links to provide fail-over, redundancy and performance enhancements
- **Teaming**: is similar to bonding, except teaming uses a userspace service named teamed. The Teaming configuration method has been listed as deprecated and is scheduled to be removed in the future.
## `/etc/host`
- Holds a local database of hostnames and IP addresses, a set of records which map IP addresses with corresponding hostnames and aliases.
- Other host-related files in `/etc` are `/etc/hosts.deny` and `/etc/hosts.allow`.
## DNSs and Name Resolution
- The act of translating hostnames to the IP addresses of their hosts.
![[image-4.png|265x342]]
### Utilities
- `dig`: generates the most information and has many options.
- `host`
- `nslookup`: older and not recommended; can have wrong answers, especially with Domain Name System Security Extensions (DNSSEC)
## Network troubleshooting
- **IP config**: use `ifconfig` or `ip` to see if the interface is up.
- **Network driver**: if the interface cannot be brought up, maybe the correct device driver for the network card(s) is not loaded. Check with `lsmod` if the network driver is loaded as a kernel module, or by examining relevant pseudofiles in `/proc` and `/sys`, such as `/proc/interrupts` or `/sys/class/net`.
- **Connectivity**: using `ping` to see the network is visible, `traceroute` can follow packets through the network, while `mtr` can do this in a continuous fashion.
- **Default gateway and routing configuration**: use `route -n` to see the routing table is configured correctly.
- **Hostname resolution**: use `dig` or `host` on a URL to see if DNS is working properly.