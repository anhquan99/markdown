# Resource Limits
- To set limit in the system, configure in `/etc/security/limits.conf`.
- Use `ulimit -a` to see current resource limit of the user.
	- A user can only raise their limit only once but cannot exceed the hard limit, after that user can't.
	- A user can always lower their limit.
- 