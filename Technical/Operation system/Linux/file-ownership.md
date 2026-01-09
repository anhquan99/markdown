# File ownership
| Command  | Full command name        | Usage                                                                                                                                          |
| -------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `chown`  | **Ch**ange **own**er     | Change user ownership of a file or directory, only superuser can do                                                                            |
| `chgrp`  | **Ch**ange **gr**ou**p** | Change group ownership only to groups you belong to                                                                                            |
| `chmod`  | **Ch**ange **mod**e      | Change permissions on the file, which can be done separately for **owner**, **group** and the rest of the **world** (often named as **other**) |
| `users`  |                          | List users                                                                                                                                     |
| `groups` |                          | List groups                                                                                                                                    |
- `-R` option for recursive.
```bash
sudo chown jane somefile # change only ownership
sudo chown wally:cleavers somefile # change ownership and group at the same time
```
## File permission
- Only the owner or root user can change permission of file or directory.
```shell
ls -l
# drwxr-xr-x   3 user  group        96 Nov 21  2024 bin
```
- The first character in the permissions shown when using the `ls -l` command is type of entry it is:

| File type        | Identifier |
| ---------------- | ---------- |
| Directory        | d          |
| Regular file     | -          |
| Character device | c          |
| Link             | l          |
| Socker file      | s          |
| Pipe             | p          |
| Block device     | b          |

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
	- `-`: no permission
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
- The permission is evaluated from left to right.
	- Example: If the owner in a group with the permission `-r--rw---` that owner can not write to the file because the owner doesn't have write permission even thought that owner in the group.
## Directory permission
- Like file permission, but directory doesn't have any content, so the permission is slightly different
	- `r`: read
	- `w`: write to directory
	- `w`: execute into directory
	- `-`: no permission
## `umask`
- The `umask` command on Linux is a powerful tool that sets the default file or directory permissions when a new file or directory is created.
## SUID
- SUID - **Set User ID** is a special permission that allows an executable file to run with the privileges of the **file's owner**, rather than the user who executed it.
```shell
chmod 4664 suidfile
# -rwSrw-r-- 1 test test 0 Jan  9 09:35 suidfile
# the S bit show us the SUID is enable for this file
# anybody else execute the file will execute as test user
# but owner does not have execute permission

chmod 4764 suidfile
# -rwsrw-r-- 1 test test 0 Jan  9 09:35 suidfile
# owner now have execute permission
```
- To find SUID file use `find . -perm /4000`.
## SGID
- SGID - **Set Group ID** is similar to SUID but applies to both file and directory permissions are handled, primarily for managing group access in collaborative environments.
```shell
chmod 2664 sgidfile
# -rw-rwSr-- 1 test test 0 Jan  9 09:35 sgidfile
# the S bit show us the SGID is enable for this file
# anybody else execute the file will execute as group
# but group does not have execute permission

chmod 2664 sgidfile
# -rw-rwsr-- 1 test test 0 Jan  9 09:35 sgidfile
# group now have execute permission

chmod 6664 sgidfile # apply SUID and SGID in a file
# -rwSrwSr-- 1 test test 0 Jan  9 09:35 sgidfile
```
- To find SGID file use `find . -perm /2000`.
- To find SUID and SGID use `find . -perm /6000`
## Sticky Bit
- A special permission that can be set on directories. It restricts file deletion in that directory.
- Usage: shared file in working environment, the rule allows to delete your own directories but cannot delete other people directory.
```shell
chmod -t stickydir # shorthand command

chmod 1777 stickydir
# drwxrwxrwt 2 test test 4096 Jan  9 10:03 stickydir
# the t bit is show us the sticky bit 

chmod 1666 stickydir
# drw-rw-rwT 2 test test 4096 Jan  9 10:03 stickydir
# no execute permission with T bit


```
## Filesystem ACLs
- **POSIX ACLs** (Access Control Lists) extend the simpler user, group, and world system.
- Particular privileges can be granted to specific users or groups of users when accessing certain objects or classes of objects. Files and directories can be shared without using 777 permissions.
- While the Linux kernel enables the use of ACLs, it still must be implemented as well in the particular filesystem. All major filesystems used in modern Linux distributions incorporate the ACL extensions, and one can use the gitoption `-acl` when mounting. A default set of ACLs is created at system install.
- Use `getfacl/setfacl` to get/set ACLs.
## Commands
### List files and directories
- `ls`: **l**i**s**t
### `chmod`
#### Add permission with `+`

|        | Option | Example              |
| ------ | ------ | -------------------- |
| user   | `u+`   | `u+w / u+rw / u+rwx` |
| group  | `g+`   | `g+w / g+rw / g+rwx` |
| others | `o+`   | `o+w / o+rw / o+rwx` |

```shell
chmod u+w filename
```
#### Remove permission with `-`

|        | Option | Example              |
| ------ | ------ | -------------------- |
| user   | `u-`   | `u-w / u-rw / u-rwx` |
| group  | `g-`   | `g-w / g-rw / g-rwx` |
| others | `o-`   | `o-w / o-rw / o-rwx` |
```shell
chmod u-w filename
```
#### Set exact permissions

|        | Option | Example              |
| ------ | ------ | -------------------- |
| user   | `u-`   | `u=w / u=rw / u=rwx` |
| group  | `g=`   | `g=w / g=rw / g=rwx` |
| others | `o=`   | `o=w / o=rw / o=rwx` |
```shell
chmod u=rwx filename
```
