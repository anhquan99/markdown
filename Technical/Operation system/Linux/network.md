# Network

| Networking Tools | Description                                                                                               |
| ---------------- | --------------------------------------------------------------------------------------------------------- |
| `ethtool`        | Queries network interfaces and can also set various parameters such as the speed                          |
| `netstat`        | Displays all active connections and routing tables; useful for monitoring performance and troubleshooting |
| `nmap`           | Scans open ports on a network; important for security analysis                                            |
| `tcpdump`        | Dumps network traffic for analysis                                                                        |
| `iptraf`         | Monitors network traffic in text mode                                                                     |
| `mtr`            | Combines functionality of ping and traceroute and gives a continuously updated display                    |
| `dig`            | Tests DNS workings; a good replacement for host and `nslookup`                                            |
| `ip`             | Show / manipulate routing, network devices, interfaces and tunnels                                        |
| `resolvectl`     |                                                                                                           |
## CIDR
- **C**lassless **I**nter-**D**omain **R**outing
- The CIDR of:
	- IPv4 has 32 bits
	- IPv6 has 128 bits
- Example of IPv4: `192.168.1.101/24`
	- The `192.168.1` is the prefix of the address with 24 bits
	- The 101 is the device on the network with 8 remain bits
- Example of IPv6: `2001:0db8:0000:0000:0000:ff00:0042:8329`
	- Short form: `2001:db8:ff00:42:8329`
## Interfaces
- Loopback: localhost.
## Network config files
- Network configuration files are essential to ensure that interfaces function correctly.
- They are located in the `/etc` directory tree.
- Edit resolver in `/etc/systemd/resolved.conf`.
- Check the example files in the `/usr/share/doc/`.
## Utilities
### `route` or `ip route`
- Used to view or change the IP routing table to add, delete, or modify specific (static) routes to specific hosts or networks.
### `traceroute`
- Used to inspect the route which the data package takes to reach the destination host, which makes it quite useful for troubleshooting network delays and errors.
### `wget`
- Used to download files with:
	- Large file downloads
	- Recursive downloads, where a web page refers to other web pages and all are downloaded at once
	- Password-required downloads
	- Multiple file downloads.
### `curl`
- Used to obtain information about a URL.
### `ss` or `netstat`
- Used for inspecting programs are currently running and waiting for incoming network connections.
- `netstat` is out of support soon and is not available on some system.
```shell
ss -ltup # get programms are litening on TCP and UDP connection
```
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
- Edit host file in `/etc/hosts`.
## DNSs and Name Resolution
- The act of translating hostnames to the IP addresses of their hosts.
![[image-4.png|265x342]]
### Utilities
- `dig`: generates the most information and has many options.
- `host`
- `nslookup`: older and not recommended; can have wrong answers, especially with Domain Name System Security Extensions (DNSSEC)
- `netplan`: an utility that sends instructions to Systemd network daemon called Systend-Networkd.
	- The configuration is in `/etc/netplan/`.
	- The configuration is process in alphabet order.
## Network troubleshooting
- **IP config**: use `ifconfig` or `ip` to see if the interface is up.
- **Network driver**: if the interface cannot be brought up, maybe the correct device driver for the network card(s) is not loaded. Check with `lsmod` if the network driver is loaded as a kernel module, or by examining relevant pseudofiles in `/proc` and `/sys`, such as `/proc/interrupts` or `/sys/class/net`.
- **Connectivity**: using `ping` to see the network is visible, `traceroute` can follow packets through the network, while `mtr` can do this in a continuous fashion.
- **Default gateway and routing configuration**: use `route -n` to see the routing table is configured correctly.
- **Hostname resolution**: use `dig` or `host` on a URL to see if DNS is working properly.
## Network models
### Bridge
- Allows computers on 2 separate networks to talk to each other as if they are part of the same network. **Two or more networks**.
- Terms:
	- Bridge can be called control
	- Network interface can be called port
![[image-38.png]]
### Bonds
- Combines multiple physical network devices into 1 logical interface. **Two or more connections to a network**.
- Benefits:
	- **Resilience:** Maintains connectivity even if one network interface fails.
	- **Increased Throughput:** Aggregates multiple network cards to achieve higher data transfer speeds.
	- **Enhanced Reliability:** Offloads traffic to available interfaces if one experiences a slowdown.
- Detail content of a bond interface is in `/proc/net/bonding/bond0`.
- Bonding modes:

|     | Name                                | Code          | Note                                                                 |
| --- | ----------------------------------- | ------------- | -------------------------------------------------------------------- |
| 0   | round-robin                         | balance-rr    |                                                                      |
| 1   | active-backup                       | active-backup |                                                                      |
| 2   | XOR                                 | balance-xor   |                                                                      |
| 3   | broadcast                           | broadcast     |                                                                      |
| 4   | IEEE 802.3ad                        | 802.3ad       | Helps increase network transfer rates above single interface support |
| 5   | adaptive transmit loading balancing | balance-tlb   |                                                                      |
| 6   | adaptive load balancing             | balance-alb   |                                                                      |

![[image-39.png]]
## Reverse proxy
- A reverse proxy acts as an intermediary between client requests and the backend web server. Rather than the client directly communicating with the web server, the reverse proxy intercepts the request and forwards it.
![[image-42.png]]
## Load balancing
- Load balancing distributes incoming web requests across multiple servers, ensuring that no single server becomes overwhelmed.
![[image-43.png]]
## `nginx`
- Configuration folder is `/etc/nginx/sites-available`.
- Header parameter configuration file `/etc/nginx/proxy_params`.
- Enable `nginx` configuration by creating a symbolic link in the `/etc/nginx/sites-enabled` directory.
	- Before apply the change, the configuration can be tested with `sudo nginx -t`.
	- Then reload the service `sudo systemctl reload nginx.service`.
## NTP
- Short for **N**etwork **T**ime **P**rotocol.
- Tools:
	- `timedatectl` for time zone stuff
	- `systemd-timesyncd` for synchronize the time
		- The setting of this tool is in `/etc/systemd/timesyncd.conf`.
## `ssh`
### Server
- Configuration for `ssh` daemon is in `/etc/ssh/sshd_config`.
- Use `man sshd_config` to see the manual page of the `sshd` config.
- Addition config files are in `/etc/ssh/sshd_config.d/`. If there is any config file, it will override the configuration of the main file `/etc/ssh/sshd_config`.
### Client
- General configuration file is in `/etc/ssh/ssh_config`. This file is not recommended to edit because it will be overridden in the future update.
	- To configure the `ssh` client persistently, create a config file in `/etc/ssh/ssh_config.d`.
- Each user has the `ssh` folder in the `~/.ssh`. Create a config file with name `config`
	- On Windows this folder is `C:\Users\{username}\.ssh`.
- Use `man ssh_config` to see the manual page of the `ssh` config.
### Login with ssh key
```shell
# generate a ssh key
ssh-keygen

# copy to client machine
ssh-copy {user}@{ip}

# or you can manually copy content of .pub file to file ~/.ssh/authorized_keys
```
## Commands
```shell
# show ip address of interfaces
ip address
# or
ip addr
# or
ip a
# add color to it with -c option
ip -c a

# bring up/down an interface
sudo ip link set dev {interface} up/down
# assign ip address to an interface
sudo ip addr add {cird} dev {interface}
# delete ip address of an interface
sudo ip addr remove {cird} dev {interface}

# get netplan
netplan get
# apply config
netplan apply
# try config to ensure config is correct
netplan try --timeout {time}

```