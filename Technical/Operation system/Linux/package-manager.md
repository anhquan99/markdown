# Package Manager
## Package types
- **Binary packages**: contains files for deployment, including executable files and binaries. These are architecture-dependent.
- **Source packages**: are used to generate binary packages; one should always be able to rebuild a binary package from the source package. One source package can be used for multiple architectures.
- **Architecture-Independent packages**: contain files and scripts that run under script interpreters, as well as documentation and configuration files.
- **Meta packages**: are groups of associated packages that collect everything needed to install a relatively large subsystem, such as a desktop environment, or an office suite, etc.
## Debian packaging
- `dpkg` is the underlying package manager the Debian family, which used for install, remove, and build packages but it does not automatically download and install packages and satisfy their dependencies.
![[Pasted image 20241110130753.png|200]]
- Higher-level package management system is the Advanced Package Tool (APT) system.
## Red Hat Package Manager (RPM)
![[Pasted image 20241110131120.png|200]]
## openSUSE's YaST Software Management
- The Yet another Setup Tool (YaST) software manager is similar to other graphical package managers.
![[Pasted image 20241110131310.png|200]]
## Tool level
### Low level
- Helps install or remove a single package, or a list of packages each one of which is individually and specifically named.
- Dependencies are not fully handled, only warned about or produce an error:
	- If another package needs to be installed first, installation will fail.
	- If the package is needed by another package, removal will fail.
- The **rpm** and **dpkg** utilities play this role for the packaging systems that use them.
### High level
- This solves the dependency problems:
	- If another package or group of packages needs to be installed before software can be installed, such needs will be satisfied.
	- If removing a package interferes with another installed package, the administrator will be given the choice of either aborting, or removing all affected software.
- The `dnf` and `zypper` utilities (and the older `yum`) take care of the dependency resolution for `rpm` systems, and `apt` and `apt-cache` and other utilities take care of it for `dpkg` systems.
## Notes
- Synaptic Package Manager