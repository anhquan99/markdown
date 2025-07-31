# Namespaces
- Namespaces are for isolation
## PID
- Isolate processes from each other
- One process cannot see others
- Process can exist multiple times, once in every namespace
## Mount
- Restrict access to mounts or root filesystem
## Network
- Only access certain network devices, firewall, routing rules and socket port numbers.
- Not able to see traffic or contact all endpoints
## User
- Different set of user ids used
- User (0) inside one namespace can be different from user (0) inside another
- Don't use the host-root user (0) inside a container