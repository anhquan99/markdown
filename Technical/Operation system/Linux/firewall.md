# Firewall
- A firewall is a network security system that monitors and controls all network traffic, applies rules for incoming, outgoing network connections and package and builds flexible barriers depending on the level of trust and network topography of a given connection.
## Packet filtering
- Each packet has:
	- **Header**: contains information about destination and source addresses, what kind of packet it is, and which protocol it obeys, various flags, which packet number this is in a stream, and ​all sorts of other metadata about transmissions
	- **Payload**: contains data
	- **Footer**: same as header
- Packet filtering intercepts packets at one or more stages in the network transmission, including application, transport, network, and datalink.
## Firewall generations
- Early firewalls (late 1980s) were based on **packet filtering**.
- The next generation of firewalls were based on **stateful filter**, which also examine the connection state of the packet. DDOS can bombard this kind of firewall.
- The third generation of firewalls is called **Application Layer Firewalls**, and are aware of the kind of application and protocol the connection is using. They can block anything which should not be part of the normal flow.​
## `firewalld` and `firewall-cmd`
- `firewalld` is the *Dynamic Firewall Manager*, which utilizes network/firewall zones which have defined levels of trust for network interface or connections. 
- Configuration files are kept in the `/etc/firewalld` and `/usr/lib/firewalld` directories, the files in `/etc/firewalld`override those in the other directory and are the ones a system administrator should work on.
- The command line tool is `firewall-cmd`.
### Zones
- `firewalld` works with zones, each of which has defined level of trust and a certain know behavior for incoming and outgoing packets. Each interface belongs to a particular zone (normally, it is NetworkManager which informs `firewalld` which zone is applicable), but this can be changed with `firewall-cmd` or GUI.

| Zone                     | Behavior                                                                                                                           |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| drop                     | All incoming packets are dropped with no reply. Only outgoing connections are permitted.                                           |
| block                    | All incoming network connections are rejected. The only permitted connections are those from within the system.                    |
| public                   | Do not trust any computers on the network; only certain consciously selected incoming connections are permitted.                   |
| external                 | Used when masquerading is being used, such as in routers. Trust levels are the same as in public.                                  |
| dmz (Demilitarized Zone) | Used when access to some (but not all) services are to be allowed to the public. Only particular incoming connections are allowed. |
| work                     | Trust (but not completely) connected nodes to be not harmful. Only certain incoming connections are allowed.                       |
| home                     | You mostly trust the other network nodes, but still select which incoming connections are allowed.                                 |
| internal                 | Similar to the work zone.                                                                                                          |
| trusted                  | All network connections are allowed.                                                                                               |
```ad-note
You should not use a more open zone than necessary.
```
## Management
### Source
- A particular network address can be bound to a network interface.
```bash
# permanent add ip address of 192.168.1.x to trusted zone
sudo firewall-cmd --permanent --zone=trusted --add-source=192.168.1.0/24
```
### Service and port
- A service and port can be associated with a zone.
- Services can be edited in the files in `/etc/firewalld/services`.
```bash
# list all services available
sudo firewall-cmd --get-service

# list services in a particular zone
sudo firewall-cmd --list-services --zone=public

# add service to a zone
sudo firewall-cmd --permanent --zone=home --add-service=dhcp
sudo firewall-cmd --reload # need to reload to make changes effective

# same with port
sudo firewall-cmd --zone=home --add-port=21/tcp
sudo firewall-cmd --zone=home --list-ports

# port redirection and NAT
sudo firewall-cmd --zone=external --add-forward-port=port=80:proto=tcp:toport=8080
```
## Network Address Translation (NAT)
- NAT is a method the firewall uses to change the packet source or destination address as it enters or exits the firewall. There are several types of NAT:
	- **DNAT** (Destination Network Address Translation)
	- **SNAT** (Source Network Address Translation)
	- **Masquerade**, a variation of **SNAT**
### DNAT
- Alters the end destination (the **to** IP address) and possibly the destination port, as the packet enters the system from the Internet.
	- When a packet is sent, it will have the public IP address provided by DNS
	- The public IP address will be that of the firewall
	- Changing the destination of the packet is necessary to allow the packet to move through the firewall to the internal network to the desired service
- The **DNAT** firewall rule is normally placed in the pre-routing chain of the **NAT** table.
- Since firewalls are typically pass-through devices (few local services), the packet is examined and the destination port examined to see if there is an applicable rule. Our rule will examine the packet for:
	- Destination IP address; it should be that of the firewall
	- Destination port number; it will be the port number of the service (http=80)
	- The **DNAT** rule will change the destination IP address to the address of the web server behind the firewall
	- The packet will be forwarded to the web server behind the firewall
	- The web server accepts and processes the packet and sends its reply
### SNAT and Masquerade
- SNAT changes the source (the **from**) IP address on the packet as it leaves the system on the way to the Internet.
	- Translation is required to protect the addresses used on internal networks from exposure to the Internet
	- Most internal networks are private networks and not routable
- If the IP on the outbound interface is provided by DHCP, the **Masquerade** option is used to implement **SNAT**, **Masquerade** is requires more compute resources than **SNAT**. But if the outbound interface is static then the SNAT can be used and public IP address is specified.
![[image-1.png]]
- The **SNAT** or **Masquerade** firewall rule is normally placed in the post-routing chain of the **NAT** table. Normally, post-routing is the last examination or modification point as the packet leaves the system on the way to the Internet.
	- The destination address of the packet is that of the remote system that initiated the connection
	- The source address of the packet will be changed from the internal address of the service to that of the firewall’s public facing adapter
## `ufw`
- The `ufw` process in order with the numbered rule.
```shell
# get rules
ufw status
# get rules with numbered
ufw status numbered

# allow only for port protocol
ufw allow {port}/{protocol}
# allow ip can request to any port
ufw allow from {ip} to any {port}
# allow cidr
ufw allow from {cidr} to any port {port}

# deny incoming network from ip
ufw deny from {ip}

ufw enable

# insert rule with number
ufw insert {number} {rule}

# delete a rule with number
ufw delete {numbered rule}
# delete a rule
ufw delete allow 22

# apply network rule on specific interface
ufw allow/deny out on {interface} to {ip}

# example of a network fine tune rule that take the ip of the current machine 10.0.0.100 to external ip 10.0.0.145
# inbound rule
ufw allow in on {interface} from 10.0.0.145 to 10.0.0.100 proto {protocol}
# outbound rule
ufw allow out on {interface} from 10.0.0.100 to 10.0.0.145 proto {protocol}
```