# Workload Consideration
## Before downloading image
- The best place to begin security is before software has ever been accessed. Only allowing trusted repositories is the first step. Even then, utilize keys and hashes to make sure the package has not been modified after the original programmer finished their work.
- Using as small a base image as possible to lower the potential complexity and better analyze what issues the microservice could contain.
## Before running a container
- Static Analysis checks issue with code prior to running it.
- Dynamic Analysis checks how software actually runs in a protected, or sandbox environment.
- As the code will be running, and perhaps compromising the system, there is greater risk to dynamic analysis. If the images are from trusted repositories, and static analysis has not found any issues, it is still important to analyze the program as it runs. This task would typically be done on a development or non-essential cluster, not in production.
- Images could be downloaded and analyzed, or scanned, via static and dynamic analysis, then added to the local repository. Another way to scan images would be through the use of a Dynamic Admission Controller. You would first set up a tool like Sysdig or Aqua, and then add a `ValidatingWebhookConfiguration`, which uses a webhook whenever a `CREATE` or `UPDATE` call is made. Be aware that this will add a lot of time to the creation process, as the scan may download the image and scan for vulnerabilities. A 30 second timeout may expire before the scan completes, causing the creation process to fail.
## Tools
### Static analysis
- Docker Bench
- kube-bench
- Clair
- Trivy
### Dynamic analysis
- `perf` and `ftrace` Linux commands
- Tracee
- eBPF
- Falco
## Leverage admission controller
- The optimal situation is when scanning is done prior to the container or software being allowed into the production environment. Dynamic admission controller can be leveraged, every API call to create an image would then reference an outside tool in order to scan and approve an image prior to that image being accepted.
- While this setup is beneficial for checking all images, it also adds a lot of latency and resource usage to the environment. Every time the same, previously-cleared, image is requested again, it will be checked. The amount of latency depends on access to the external source and the size and complexity of the image.
- A common option is to leverage a dynamic admission controller to insert an `initContainer`: containing a scan or verification tool into the pod spec. Then the load is distributed across all the workers. Only if the `initContainer` scans and has a zero exit code, is the rest of the pod spec passed to the container engine for execution.
## Changing containers
- Consideration should be given to containers with read-write filesystems for source data, the ability to elevate privileged users and other features.
- It's a good idea to regularly schedule evaluation of non-immutable containers and reevaluate to ensure they conform to current security restrictions.
### Tools
- SELinux - Security Enhanced Linux
- AppArmor
- TOMOYO - 
- Smack - Simplified Mandatory Access Control Kernel
### `seccomp`
- Secure computing mode (`seccomp`) is a Linux kernel feature that can be used to restrict the actions available within the container.
- With Mode 2, the BPF and eBPF filters can be used to determine which system calls are to be allowed. Once the eBPF program is constructed and installed into the kernel, every call goes through the filter.
- In k8s, you can use `seccomp` for several features, such as `syscall` auditing and denial of disallowed calls, which will cause the pod to enter a `CrashLoopBackoff` state.
### SELinux
- SELinux is a set of security rules that are used to determine which processes can access which files, directories, ports, and other items on the system. As all objects in Linux are some sort of file and everything that is done is some sort of process, SELinux can be used to control everything.
- SELinux works with 3 conceptual quantities:
	- **Context**: these are labels to files, processes and ports. Examples of contexts are SELinux user, role and type. 
	- **Rules**: they describe access control in terms of contexts, processes, files, ports, users, etc.
	- **Policies**: they are a set of rules that describe what system-wide access control decisions should be made by SELinux.
- A SELinux context is a label used by a rule to define how users, processes, files and ports interact with each other. As the default policy is to deny any access, rules are used to describe allowed actions on the system. Each of the actions must be allowed via the Access Vector Cache.
#### Enforcement modes
- SELinux enforcement modes are selected in a file (usually `/etc/selinux/config` or `/etc/sysconfig/selinux`).
- Modes:
	- **Enforcing**: access is denied according to the policy, all violations are logged. If violation in the `dontaudit` list then it is not logged.
	- **Permissive** : warns about operations that would have been denied in enforcing mode. Same as violation happens in the `dontaudit` list.
	- **Disabled**: completely disables the SELinux kernel and application code leaving the system without any of its protections. The only way to enter this mode is by reboot.
- Use `getenforce` and `setenforce` to see or set current mode.
- The `sestatus` utility can display the current mode and policy. The `seinfo` command can show details if a policy file is available.
- Disable SELinux:
	- **Configuration file**: set `SELINUX=disabled` in the configuration file and reboot.
	- **Kernel parameter**: add `selinux=0` to the Kernel parameter list when rebooting.
#### Default SELinux policies
- Multiple policies are allowed, but only 1 can be active at a time.
- Changing the policy may require a reboot of the system and a time-consuming re-labeling of filesystems contents.
- Default policies:
	- **targeted**: the default policy in which SELinux is more restrictive to targeted processes. User processes and init processes are not targeted, while network service processes are targeted. SELinux enforces memory restrictions for all processes, which reduces the vulnerability to buffer overflow attacks.
	- **minimum**: a modification of the targeted policy where only selected processes are protected.
	- **Multi-Level Security (MLS)**: much more restrictive; all processes are placed in fine-grained security domains with particular policies
#### Context inheritance
- Newly created files inherit the context from their parent directory, but when moving or copying files, it is the context of the source directory which may be preserved, which can cause problems.
- Use `restorecon` to reset file contexts, based on parent directory settings.
### AppArmor
- AppArmor is an alternative LSM (Linux Security Module) to SELinux, which supplements the traditional UNIX **Discretionary Access Control (DAC)** model by providing **Mandatory Access Control (MAC)**.
- AppArmor includes a learning mode, in which violations of the profile are logged, but not prevented. This log can then be turned into a profile, based on the program’s typical behavior.
#### Using with k8s
- AppArmor must be available on the node where assigned. There is not a native process for k8s to load policies. As a result, you need to ensure policies are loaded on every node where AppArmor-required pods are scheduled, and the scheduler is unaware of which nodes have profiles.
- Adding profiles could be done during node installation with a tool like Ansible or Puppet, at least for some of the nodes. If only some nodes will have profiles installed, you could use a `NodeSelector`or a taint to ensure the scheduler chooses the appropriate node. Another solution would be to deploy a `DaemonSet` and allow the pod the ability to modify the host and add profiles. This would put the responsibility into the cluster administrators, if different from those responsible for operating system configuration and security.
#### Modes and profiles
- Profiles restrict how executable programs, which have pathnames on your system can be used.
- Processes can run in modes:
	- **Enforce mode**: applications are prevented from acting in ways which are restricted. Attempted violations are reported to the system logging files. This is the default mode. A profile can be set to this mode with `aa-enforce`.
	- **Complain**: polices are not enforced, but attempted policy violations are reported. This is also called learning mode. A profile can be set to this mode with `aa-complain`.
- Profiles in AppArmor is stored in `/etc/apparmor.d`.
#### Complex profiles
- If you know the specific function, perhaps to want to run a new containerized application, you can use `aa-genprof`. If passed the location of existing profiles, the tool will create a new complain mode profile, send a starting mark to the system log, and prompt the running of the program in a different terminal. While the program runs, you can use the Scan feature to parse the log and add to the profile. When all program operations have been scanned, use the Finish and cause the complain mode profile to become an enforce mode.
- To generate a systemic profile, you can run `aa-logprof`. When run, all entries in the system log are presented to the user to choose one of several options: `(A)llow, (D)eny, (I)gnore, (N)ew, (G)lob last piece, (Q)uit`. This process can continue for a long time as normal system operation takes place. When **Quit** is chosen, the profile is persisted to disk and AppArmor is reloaded if active.