# Package Manager
## Package
- An archive containing the files needed by a piece of software.
- It may include binaries, configuration files, documentation, etc.
## Package types
- **Binary packages**: contains files for deployment, including executable files and binaries. These are architecture-dependent.
- **Source packages**: are used to generate binary packages; one should always be able to rebuild a binary package from the source package. One source package can be used for multiple architectures.
- **Architecture-Independent packages**: contain files and scripts that run under script interpreters, as well as documentation and configuration files.
- **Meta packages**: are groups of associated packages that collect everything needed to install a relatively large subsystem, such as a desktop environment, or an office suite, etc.
## Debian packaging

![[Pasted image 20241110130753.png|200]]
### `dpkg`
- `dpkg` is the underlying package manager the Debian family, which used for install, remove, and build packages, but it does not automatically download and install packages and satisfy their dependencies.
```bash
dpkg -s dpkg | grep -i version # see what version of a particular package is installed
sudo dpkg -V # verify all packages on the system
dpkg -V package # verify the installed package's integrity
dpkg -I <filename>.deb # show information about a package file
dpkg -c <filename>.deb # list files in a package file
dpkg -s wget # show information about an installed package
sudo dpkg -i <package-name>.deb # install or upgrade package
sudo dpkg -r package # remove installed package
sudo dpkg -P package # remove installed package, including its config files. P stand for purge
dpkg --listfiles <package> # list file in the package
```
### `apt`
- Higher-level package management system is the Advanced Package Tool (APT) system.
- It can automatically resolve dependencies when installing, updating and removing packages. 
- It accesses external software repositories, synchronizing with them and retrieving and installing software as needed.
```bash
apt search <package>
apt show <package>
apt depends <package>
apt search <package-file-name> # Search the repository for a file name
apt list <package> # list all files in package
apt install <package>
apt remove <package>
apt autremove <package> # remove all dependency
sudo apt --pruge remove <package>
apt update # sync package index files with their source. The indexes of available packages are fetched from the location(s) specified in /etc/apt/sources.list
sudo apt upgrade # upgrade installed packages
sudo apt dist-upgrade
sudo apt autoremove # gets rid of any packages not needed anymore, such as older Linux kernel versions:
sudo apt clean # cleans out cache files and any archived package files that have been installed
```
- `apt full-upgrade` (the correct equivalent for `apt-get dist-upgrade`) applies package upgrades as well if they require either the installation of new packages or the removal of conflicting installed packages. Basically it will apply all package upgrades including those with changed dependencies.
- The configuration for package is in the `/etc/apt/source.list` or `/etc/apt/sources.list.d/ubuntu/sources` for later version.
```shell
Types: deb # tell the package manager expect Debian packages in this repository
URIs: http://archive.ubuntu.com/ubuntu # where the package manager to look for package
Suites: noble noble-updates noble-backports # suite a set of packages that are associated with a specific release or version of the distribution
Components: main universe restricted multiverse # components are categories of packages that are grouped together based on their licensing, functionality, or other criteria
# main: contains free, open-source software and officially supported by Ubuntu
# restricted: free and open-source software, but they may have some restrictions on their use or redistribution
# universe: free and open-source software, but not officially supported by Ubuntu
# multiverse: not free and open-source
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg # public key to verify the package has not been modified
```
## Red Hat Package Manager (RPM)
![[Pasted image 20241110131120.png|200]]
### `rmp`
- Package naming format for binary package: `<name>-<version>-<release>.<distro>.<architecture>.rpm`
#### Queries
 - All `rpm` inquiries include the `-q` option, which can be combined with numerous other query options:
	- `-f`: allows you to determine which package a file came from
	- `-l`: lists the contents of a specific package
	- `-a`: all the packages installed on the system
	- `-i`: information about the package
	- `-p`: run the query against a package file instead of the package database

| Task                                                                                 | Command                             |
| ------------------------------------------------------------------------------------ | ----------------------------------- |
| Which version of a package is installed?                                             | `$ rpm -q bash*`                    |
| Which package did this file come from?                                               | `$ rpm -qf /bin/bash`               |
| What files were installed by this package?                                           | `$ rpm -ql bash`                    |
| Show information about this package.                                                 | `$ rpm -qi bash`                    |
| Show information about this package from the package file, not the package database. | `$ rpm -qip foo-1.0.0-1.noarch.rpm` |
| List all installed packages on this system.                                          | `$ rpm -qa`                         |
- A couple of other useful parameters are `--requires` and `--whatprovides`. The `--requires` option will return a list of prerequisites for a package, while the `--whatprovides` option will show what installed package provides a particular requisite package. Below are a few sample commands:
```bash
rpm -q --requires bash
rpm -qp --requires foo-1.0.0-1.noarch.rpm  
rpm -q --whatprovides libc.so.6
```
#### Verify packages
- The `-V` option to `rpm` allows you to verify whether the files from a particular package are consistent with the system’s RPM database. Use the `rpm -Va` command to verify all packages on the system.
	- `S`: filesize differs
	- `M`: mode differs (permissions and file type)
	- `5`: MD5 sum differs
	- `D`: device major/minor number mismatch
	- `L`: readLink path mismatch
	- `U`: user ownership differs
	- `G`: group ownership differs
	- `T`: mTime differs
#### Install packages
- RPM performs a number of tasks when installing a package:
	- Dependency checks
	- Conflict checks
	- Commands required before installation
	- Handles configuration files with intelligent care
	- Unpacks files from package and installs them with correct attributes
	- Commands required after installation
	- Updates system RPM database
- Use `-i` option to install packages.
#### Remove packages
- Use `-e` option to remove packages, it can be used with the `--test` option to see if it can be removed successfully.
- Use `-vv` option to remove verbosely.
```ad-warning
Never remove the rmp package itself. The only way to fix this problem is to re-install the operting system, or booting into a rescue env.
```
#### Update packages
- Use `-U` option to upgrade packages.
- When upgrading, the already installed package is removed after the newer version is installed. The one exception is the configuration files from the original installation, which are kept with a `.rpmsave` extension.
- If you want to downgrade with `rpm -U` (that is, to replace the current version with an earlier version), you must add the `--oldpackage` option to the command line.
#### Freshening packages
- The way this works is:
	- If an older version of a package is installed, it will be upgraded to the newer version in the directory.
	- If the version on the system is the same as the one in the directory, nothing happens.
	- If there is no version of a package installed, the package in the directory is ignored.
- The `-F` option is useful when you have downloaded several new patches and want to upgrade the packages that are already installed, but not install any new ones.
### `yum` and `dnf`
- `dnf` replaced `yum` during the RHEL/CentOS 7 to 8 transition.
- `dnf` is backwards compatible - almost all common `yum` commands still work.
### `zypper`
- `zypper` is the command line tool for installing and managing packages in SUSE Linux and openSUSE. It is very similar to `dnf` in its functionality and even in its basic command syntax, and also works with `rpm` packages.
- Sometimes, a number of `zypper` commands must be run in a sequence. To avoid re-reading all the databases for each command, you can run `zypper` in shell mode using `sudo zypper shell`.
## openSUSE's YaST Software Management
- The Yet another Setup Tool (YaST) software manager is similar to other graphical package managers.

![[Pasted image 20241110131310.png|200]]
## Tool level
### Low level
- Helps install or remove a single package, or a list of packages each one of which is individually and specifically named.
- Dependencies are not fully handled, only warned about or produce an error:
	- If another package needs to be installed first, installation will fail.
	- If the package is needed by another package, removal will fail.
- The `rpm` and `dpkg` utilities play this role for the packaging systems that use them.
### High level
- This solves the dependency problems:
	- If another package or group of packages needs to be installed before software can be installed, such needs will be satisfied.
	- If removing a package interferes with another installed package, the administrator will be given the choice of either aborting, or removing all affected software.
- The `dnf` and `zypper` utilities (and the older `yum`) take care of the dependency resolution for `rpm` systems, and `apt` and `apt-cache` and other utilities take care of it for `dpkg` systems.
## Notes
- Synaptic Package Manager