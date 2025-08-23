# Reduce Attack Surface
## Application
- Update Linux Kernel
- Keep up to date
- Remove not needed packages
## Network
- Network behind firewall
- Check and close open ports
## IAM
 - Run as user, not root
 - Restrict user permissions
## Node runs k8s
- **Only purpose:** run k8s components - remove unnecessary services.
- **Node recycling:**
	- Nodes should be ephemeral
	- Created from images
	- Can be recycled any time (and fast if necessary)
## Linux distributions
- Often include number of services, meant to help but widen attack surface.
- The more existing and running services, the more convenient.
## Common tools to check open surface
### Network
- `netstat`
### Port
- `netstat`
- `lsof`
### Running services
- `systemctl`
### Processes and users
- `ps`