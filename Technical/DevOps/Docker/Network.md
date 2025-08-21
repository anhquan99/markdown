# Network
- Create a virtual network for a group of container there it will talk to each other without point to any IP.
- When run multiple containers in the same network, the port is not needed to be mapped because internally containers are freely communicating to each other.
## Container connect to
- WWW
- Another container
- Local machine
## Local docker domain map to local host
- host.docker.internal
## Docker network drivers
- Docker networks support different kinds of `Drivers` which influence the behavior of the network.
- Drivers:
	- Bridge: is a default driver of docker, create a container in the same network.
	- Host: for standalone containers, isolation between container and host system is removed.
	- Overlay: multiple docker daemons (docker running on different machines) are able to connect with each other. Only  works in `Swarm` mode, which is a dated/almost deprecated way of connecting multiple containers.
	- `Macvlan`: you can set a custom MAC address to a container - this address can then be used for communication with that container.
	- None: all network is disabled.
	- Third-party-plugins: you can install third-party plugins.
#### [[cli##^7e7061|CLI]]