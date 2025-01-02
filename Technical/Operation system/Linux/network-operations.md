# Network Operations
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