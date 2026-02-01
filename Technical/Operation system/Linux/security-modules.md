# Security Modules
## Main Linux Security Modules (LSM) choices
- SELinux
- AppArmor
- Smack
- Tomoyo
- Tama
## SELinux
- Developed by NSA (National Security Administration).
- SELinux is a set of security rules that are used to determine which processes can access which files, directories, ports, and other items on the system.
- Used for more fine grain permission policy.
- SELinux need time to learn about normal activities in the system and then setup enforce policy.
- 3 conceptual quantities:
	- **Context**: these are labels to files, processes, and ports. Examples of contexts are SELinux user, role and type.
	- **Rules**: they describe access control in terms of contexts, processes, files, ports, users, etc.
	- **Policies**: they are a set of rules that describe what system-wide access control decisions should be made by SELinux.
- Enable by default on Red Hat OS but not Ubuntu.
### Enforcement modes
- These modes are selected in a file (usually `/etc/selinux/config`) whose location varies by distribution (`/etc/sysconfig/selinux`).
#### Enforcing
- SELinux code is operative and access is denied according to policy. All violations are audited and logged.
![[image-2.png|301x315]]
#### Permissive
- Enables SELinux code, but only audits and warns about operations that would be denied enforcing mode.
![[image-3.png|301x315]]
#### Disabled
- Completely disables SELinux kernel and application code, leaving the system without any of its protections.
### Utilities
- `sestatus` can display current mode and policy.
- `getenforce` and `setenforce` used to see or set current mode. But the `setenforce` does not allow you to disable SELinux completely, you can configure `/etc/selinux/config` file and set `SELINUX=disabled` or add kernel parameter `selinux=0` when rebooting.
### Policies
- Configure in the file `/etc/sysconfig/selinux` or `/etc/selinux/config`.
- Multiple policies are allowed, but only one can be active at a time.
- Common policies:
	- **Targeted**: the default policy in which SELinux is more restricted to targeted processes. User processes and init processes are not targeted, while network service processes are targeted. SELinux enforces memory restrictions for all processes, which reduces the vulnerability to buffer overflow attacks.
	- **Minimum**: only selected processes are protected.
	- **MLS**: Multi-Level-Security policy, all processes are placed in fine-grained security domains with particular policies.
### Contexts
- Contexts are labels applied to files, directories, ports, and processes. Those labels are used to describe access rules:
	- User: SELinux user, every user who logs into a Linux system is mapped to SELinux user.
	- Role
	- Type: like a protected software jail, the software can only do certain things and nothing else.
	- Level: fit more complex org, restrict based on the level.
- SELinux can use boolean to allow or disallow a set of action.
```shell
# check the permission of SELinux
ls -Z
unconfied_u:object_r:user_home_t:s0
#  user       role     type      level

# see the process permission of SELinux
ps axZ

id -Z
sudo semanage login -l 
sudo semanage user -l
getenforce # get enforce level of SELinux
sudo audit2why --all # get recorded audit log

# after learning enough, SELinux can export events to a policy file with .pp extension 
sudo audit2allow --all -M module_name
sudo setenforce 1

ps -eZ # show the process with SELinux domain
# example result:
system_u:system_r:sshd_t:s0    905 ?      00:00:00 sshd
# an example of a SELinux policy
allow sshd_t var_log_t:file { append create getattr ioctl open };
# this policy allow the process with sshd_t domain to write to a file with var_log_t domain
# you can check the domain of the log file with
ls -Z /var/log/auth.log

# change SELinux security context
sudo chcon -{type} unfined_u /var/log/auth.log # type should be u-user, r-role, t-type

# show SELinux user label
seinfo -u
# show SELinux role label
seinfo -r
# show SELinux type label
seinfo -t

# copy the label context from a file to other files
sudo chcon --refernece=/var/log/dmesg /var/log/auth.log
# restore label files and directories recusively with SELinux suggested - restore context
sudo restorecon -R /var/www/
# force restore all label
sudo restorecon -F -R /var/www/

# change default context of a file even if the restore context command is run, you need to restore it again this command does not execute the restore label
sudo semanage fcontext --add --type var_log_t /var/www/10
# list all files or directories of semanage
sudo semanage fcontext --list
# change default context of files in directories with regex
sudo semanage fcontext --add --type nfs_t "/nfs/shares(/.*)?"
```
- Usage:
	- Only certain users can enter certain roles and certain types.
	- It lets authorized users and processes do their job, by granting the permissions they need.
	- Authorized users and processes are allowed to take only a limited set of actions.
#### Context inheritance
- On SELinux-enabled systems, creating a file and moving it to another folder, will make the file can not be accessed with the incorrect context label.
#### Utilities
##### `restorecon`
- Reset file contexts, based on parent directory settings.
##### `semanage`
- `semanage fcontext` changes and displays the default context of files and directories (only changes the default settings, it does not apply them to existing objects).
##### `setroubleshoot-server`
- Collects issues at run time, log and propose solutions to prevent same issues from happening again.
- Stores raw message in `/var/log/audit/audit.log`.
- Mave messages to `/var/log/messages`.
- Use `sealert` to see detailed messages.
```shell
# install and enable SELinux on Ubuntu

# 1. disable AppArmor
sudo systemctl stop apparmor.service
sudo systemctl disable apparmor.service

# 2. install selinux-basics audit
sudo apt install selinux-basics audit

# 3. enable selinux
sudo selinux-activate
sestatus # check status of SELinux
cat /etc/default/grub # check for the SELinux is setup when booting
ls -a / # check for the file autolabel is exist

# show boolean list
sudo semanage boolean --list
# enable SELinux boolean
sudo setsebool virt_use_nfs 1
# get SELinux bool value
getsebool virt_use_nfs
# get allowed ports
sudo semanage port --list
# allow port
sudo semanage port --add --type ssh_port_t --proto tcp 2222
# delete allowed port
sudo semanage port --delete --type ssh_port_t --proto tcp 2222
```
### Domain
- 
## AppArmor
- An LSM alternative to SELinux.
- AppArmor supplements the traditional UNIX Discretionary Access Control (DAC) model by providing Mandatory Access Control (MAC).
- Allows administrators to associate a security profile to a program which restricts its capabilities.
- Is considered easier (by some but not all) to use than SELinux.
- Is considered filesystem-neutral (no security labels required).
![[image-34.png]]
### Modes
- **Enforce mode**: default mode, applications are prevented from acting in ways which are restricted. Violations are reported to the system logging files. `aa-enforce` can be used to set profile mode.
- **Complain mode**: policies are not enforced, but attempted policy violations are reported, also called the learning mode. Can be set with `aa-complain`.
- **Unconfined mode**: policies are not enforced and violation is not reported.
### Profiles
- Profiles restrict how executable programs.
- Linux distributions come with​ pre-packaged profiles, typically installed either when a given package is installed, or with an AppArmor package, such as `apparmor-profiles`. These profiles are stored in `/etc/apparmor.d`.