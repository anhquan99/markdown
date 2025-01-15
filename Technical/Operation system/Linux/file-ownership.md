# File ownership
| Command | Usage                                                                                                                                          |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `chown` | Change user ownership of a file or directory, only superuser can do                                                                            |
| `chgrp` | Change group ownership only to groups you belong to                                                                                            |
| `chmod` | Change permissions on the file, which can be done separately for **owner**, **group** and the rest of the **world** (often named as **other**) |
- `-R` option for recursive.
```bash
sudo chown wally:cleavers somefile # change ownership and group at the same time
```
## File access
- File authentication is granted depending on 1 of 3 sets of permissions, in the following order:
	1. If the requester is the file owner, the file owner permissions are used.
	2. Otherwise, if the requester is in the group that owns the files, the group permissions are examined.
	3. If that doesn't succeed, the world permissions are examined.
## File permission modes
- Permissions `rwx`
	- `r`: read
	- `w`: write
	- `w`: execute
- Groups of 3 permissions:
	- `u`: user/owner
	- `g`: group
	- `o`: others
```
rwx: rwx: rwx
 u:    g:   o
```
- Shorthand to set the permissions by using single digit suffices to specify all 3 permissions bits for each entity:
	- 4 for read
	- 2 for write
	- 1 for execute
- Single digit suffices sum:
	- 7 means `rwx`
	- 6 means `rw`
	- 5 means `rx`
```bash
# set rwx to user rx to groups and others
chmod 755 <file>
```
## `umask`
- The `umask` command in Linux is a powerful tool that sets the default file or directory permissions when a new file or directory is created.
## Filesystem ACLs
- **POSIX ACLs** (Access Control Lists) extend the simpler user, group, and world system.
- Particular privileges can be granted to specific users or groups of users when accessing certain objects or classes of objects. Files and directories can be shared without using 777 permissions.
- While the Linux kernel enables the use of ACLs, it still must be implemented as well in the particular filesystem. All major filesystems used in modern Linux distributions incorporate the ACL extensions, and one can use the gitoption `-acl` when mounting. A default set of ACLs is created at system install.
- Use `getfacl/setfacl` to get/set ACLs.