# Local Security Principles
## User accounts
- Format `username:password:UID:GID:description:homedir:shell`.
- These fields are maintained in the `/etc/passwd`:

| Field Name         | Details                                                                                                    | Remarks                                                                                                                                                                                                                                   |
| ------------------ | ---------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Username**       | User login name                                                                                            | Should be between 1 and 32 characters long                                                                                                                                                                                                |
| **Password**       | User password (or the character x if the password is stored in the `/etc/shadow` file) in encrypted format | Is never shown in Linux when it is being typed; this stops prying eyes                                                                                                                                                                    |
| **User ID (UID)**  | Every user must have a user id (UID)                                                                       | UID 0 is reserved for root user<br><br>UID's ranging from 1-99 are reserved for other predefined accounts<br><br>UID's ranging from 100-999 are reserved for system accounts and groups<br><br>Normal users have UID's of 1000 or greater |
| **Group ID (GID)** | The primary Group ID (GID); Group Identification Number stored in the `/etc/group` file                    | GID `0` is reserved for root.<br><br>`1`-`99` are reserved for system and application use.<br><br>`100`+ are for users.                                                                                                                   |
| **User Info**      | This field is optional and allows insertion of extra information about the user such as their name         | For example: **Rufus T. Firefly**                                                                                                                                                                                                         |
| **Home Directory** | The absolute path location of user's home directory                                                        | For example: `/home/rtfirefly`                                                                                                                                                                                                            |
| **Shell**          | The absolute location of a user's default shell                                                            | For example: `/bin/bash`                                                                                                                                                                                                                  |
## Types
- root
- System
- Normal
- Network
### `last`
- `last` helps show the last time each user logged into the system.
## root
- The most privileged account on a Linux/UNIX system.
- When you are signed in as, or acting as root, the shell prompt displays `#`.
![[Pasted image 20250103224243.png]]
- A regular account user can perform some operations requiring special permissions; however, the system configuration must allow such abilities to be exercised.
- `SUID` (Set owner User ID upon execution) is a special kind of file permission given to a file. Use of SUID provides temporary permissions to a user to run a program with the permissions of the file _owner_ (which may be root) instead of the permissions held by the _user_.
## `sudo`

| `su`                                                                                                                                                                             | `sudo`                                                                                                                                                                                                                                                                                                                            |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| When elevating privilege, you need to enter the root password. Giving the root password to a normal user should never, ever be done.                                             | When elevating privilege, you need to enter the user’s password and not the root password.                                                                                                                                                                                                                                        |
| Once a user elevates to the root account using `su`, the user can do anything that the root user can do for as long as the user wants, without being asked again for a password. | Offers more features and is considered more secure and more configurable. Exactly what the user is allowed to do can be precisely controlled and limited. By default the user will either always have to keep giving their password to do further operations with `sudo`, or can avoid doing so for a configurable time interval. |
| The command has limited logging features.                                                                                                                                        | The command has detailed logging features.                                                                                                                                                                                                                                                                                        |
- `sudo` has the ability to keep track of unsuccessful attempts at gaining root access. Users' authorization for using `sudo` is based on configuration information stored in the `/etc/sudoers` file and in the `/etc/sudoers.d` directory.
- Whenever `sudo` is invoked, a trigger will look at `/etc/sudoers` and the files in `/etc/sudoers.d` to determine if the user has the right to use `sudo` and what the scope of their privilege is. Unknown user requests and requests to do operations not allowed to the user even with `sudo` are reported.
- Most Linux distributions now prefer you add a file in the directory `/etc/sudoers.d` with a name the same as the user. This file contains the individual user's `sudo` configuration, and one should leave the main configuration file untouched except for changes that affect all users.
- These configuration files should be edited by `visudo`, which ensures that only 1 person is editing the file at a time.
- By default, `sudo` commands and any failures are logged in `/var/log/auth.log` under the Debian distribution family, and in `/var/log/messages` and/or `/var/log/secure` on other systems. A typical entry of the message contains:
	- Calling username
	- Terminal info
	- Working directory
	- User account invoked
	- Command with arguments
```
Jan  3 16:01:07 ubuntu sudo:     root : TTY=pts/0 ; PWD=/root ; USER=root ; COMMAND=/usr/bin/whoami
```
## Process isolation
- Linux is considered to be more secure than many other operating systems because processes are naturally isolated from each other.
- One process normally cannot access the resources of another process, even when that process is running with the same user privileges. 
- Linux thus makes it difficult (though certainly not impossible) for viruses and security exploits to access and attack random resources on a system.
- More recent additional security mechanisms that limit risks even further include:
	- Control Groups (`cgroups`)  
	    Allows system administrators to group processes and associate finite resources to each `cgroup`.
	- Containers  
	    Makes it possible to run multiple isolated Linux systems (containers) on a single system by relying on `cgroups`.
	- Virtualization  
	    Hardware is emulated in such a way that not only can processes be isolated, but entire systems are run simultaneously as isolated and insulated guests (virtual machines) on one physical host.
## Hardware device access
- Linux limits user access to non-networking hardware devices in a manner that is extremely similar to regular file access.
- Applications interact by engaging the filesystem layer under `/dev` directory that corresponds to the device being accessed.
## Password file
- Password is stored and encrypted in the `/etc/shadow` file.
- Only those with root access can read or modify this file.
- Password aging can be implemented using the `chage`, which configures the password expiry information for a user.
### `/etc/shadow`
- Contains 1 record (1 line) for each use.
```
daemon:*:16141:0:99999:7:::
```
- Each record contains fields separated by colons ( : ):
	- **username**: unique user name
	- **password**: the hashed (sha512) value of the password
	- **lastchange**: days since Jan 1, 1970 that password was last changed
	- **mindays**: minimum days before password can be changed
	- **maxdays**: maximum days after which password must be changed
	- **warn**: days before password expires that the user is warned
	- **grace**: days after password expires that account is disabled
	- **expire**: date that account is/will be disabled
	- **reserved**: reserved field
## Notes
- `/etc/skel`