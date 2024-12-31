# User accounts
- Linux is a multi-user operating system, meaning more than 1 user can log on at the same time.
## Startup files
- Files in the `/etc` directory define global settings for all users, while initialization files in the user's home directory can include and/or override the global settings.
- The standard prescription is that when you first login to Linux, `/etc/profile` is read and evaluated, after which the following files are searched (if they exist) in the listed order:
	1. `~/.bash_profile`
	2. `~/.bash_login`
	3. `~/.profile`
- Every time you create a new shell, or terminal window, you do not perform a full system login; only a file named `~/.bashrc` file is read and evaluated.
- Recent distribution sometimes do not even have `.bash_profile` or `.bash_login`.
![[Pasted image 20241231094149.png]]
## Users and groups
- All Linux users are assigned a unique user ID (`uid`), which is just an integer normal users start with a `uid` of 1000 or greater.
- Linux uses groups for organizing users. Group are collections of account with certain shared permissions, they are used to establish a set of users who have common interests for the purposes of access rights, privileges, and security considerations.
- Control of group membership is administered through the `/etc/group` file, which shows the list of groups and their members. By default, every user belongs to a default (primary) group, and all the members have the same level of access and privilege. Permissions on various files and directories can be modified at the group level, associated with names through the files `/etc/passwd and /etc/group`.
### Commands
- Use `useradd` to add new user.
- Use `userdel` to remove user, this will leave the `/home/<user>` directory intact, the `-r` option helps remove the user directory while removing user account.
- Use `id` to show information of the current user.
- Use `groupadd` to add new group.
- Use `groupdel` to remove a group.
- Use `usermode` to add user to an existing group.
### Root user
- `su` for switch or substitute user, which is used for switch user account.
- `sudo` configuration files are stored in the `/etc/sudoers` file and in the `/etc/sudoers.d/` directory.