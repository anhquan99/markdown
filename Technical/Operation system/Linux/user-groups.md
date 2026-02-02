# User Groups
- Collection of users is called group, whose members share some common purpose.
- They share certain files and directories, and maintain some common privileges.
- Groups are defined in `/etc/group`.
- Group password may be set, but only if `/etc/gshadow` exists.
```
groupname:password:GID:user1,user2,...
```
## Group management
- `groupadd`
- `groupmod`
- `groupdel`
- `gpasswd`
- `usermod`: manage a user's group memberships.
```ad-note
The **usermod -G** command is the total use list of groups, so it will _delete_ and add groups all on one command line. Non-destructive use should utilize the **-a** option, which will preserve pre-existing group memberships when adding new ones.
```
## User private groups (UPG)
- The idea behind UPGs is that each user will have their own group.
- However, UPGs are not guaranteed to be private, additional members may be added to their private group in `/etc/group`.
- By default, users whose accounts are created with `useradd` have the primary group ID equal to their user id **GID = UID** and their group name is also identical to the username.
## `visudo`
- An command helps safely edit `/etc/sudoers`.
- It validates the edit of the user to avoid mistakes in this file.
```
user/group   host=(run_as_user:run_as_group) command_list
# example:
%sudo   ALL=(ALL:ALL) ALL
# if the group is missing then it is understand as ALL 
jane   ALL=(ALL) ALL
# no password
jane   ALL=   NOPASSWD:ALL
```