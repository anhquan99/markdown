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
- 3 conceptual quantities:
	- **Context**: these are labels to files, processes, and ports. Examples of contexts are SELinux user, role and type.
	- **Rules**: they describe access control in terms of contexts, processes, files, ports, users, etc.
	- **Policies**: they are a set of rules that describe what system-wide access control decisions should be made by SELinux.
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
	- User
	- Role
	- Type
	- Level
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