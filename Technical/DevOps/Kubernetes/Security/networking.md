# Networking
## Terms and expressions
- Ingress filtering: the filtering of data connections, and connection requests not originating from the protected network. It is important to filter all ingress data not specifically required for services provided by the protected network to function.
- Egress filtering: the filtering of packets originating inside the firewall. This is an important part of enforcing acceptable use policies, and reducing the risk that any malware or infected hosts can attack hosts outside the firewall.
- Bridging: the act of aggregating multiple networks to appear as a single network, appearing as a direct connection of 1 network to the other.
- Forwarding: relaying of a packet from 1 node on a network to another. Ports can also be configured to forward on a firewall/router, allowing ingress packets on port A to be forwarded to port B on a host inside the firewall. The decision of what packet is forwarded to which port is part of a routing.
- Network Address Translation (NAT)