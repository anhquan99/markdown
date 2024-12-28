# Filesystem
## Varieties
- Linux supports:
	- `ext3`
	- `ext4`
	- `squashfs`
	- `btrfs`
- Others:
	- Windows (`ntfs, vfat, exfat)
	- SGI (`xfs`)
	- IBM (`jfs`)
	- macOS (`hfs, hfs+`)
- It is often the case that more than 1 filesystem type is used on a machine, based on considerations such as the size of files, how often they are modified, what kind of hardware they it on and what kind of access speed is needed, ...
- The most advanced filesystem types in common use are the `journaling` varieties: `ext4, xfs, btrfs and jfs`. These have many state-of-the-art features and high performance, and are not easy to corrupt accidentally.
## Partitions
- Partitions help to organize the contents of disks according to the kind and use of the data contained.
- Advantage of this kind of isolation by type and variability is that when all available space on a particular partition is exhausted, the system still operates normally.
## Mount points
- Before you can start using a filesystem, you need to mount it on the filesystem tree at a mount point. This is simply a directory (which may or may not be empty) where the filesystem is to be grafted on. Sometimes, you may need to create the directory if it does not already exist.
```ad-warning
If you mount a filesystem on a non-empty directory, the former contents of that directory are covered-up and not accessible until the filesystem is unmounted. Thus, mount points are usually empty directories.
```
- To mount a filesystem, use `mount <device-node> <folder>`.
- To unmount, use `unount <folder>` where user must be a root user.
- To enable automatically mount the filesystem when starting up, edit the `/etc/fstab` (**f**ile**s**ystem **tab**le).
## Network filesystem (NFS
### Server
- NFS uses **daemons** (built-in networking and service processes in Linux).
- The `/etc/exports` contains the directions and permissions that a host is willing to share with other system over NFS.
- After modifying the `/etc/exports`, you can use `exportnfs -av` to notify Linux about the directories you are allowing to be remotely mounted using NFS, or you can use `systemctl restart nfs` but this is heavier, as it halts NFS for a short while before starting it up again.
### Client
- `/etc/fstab` to remote access the server filesystem.
- Use `mount <servername>:/<folder> <local-folder>` to mount the other filesystem to local folder.
- You may want to use `nofail` option in `fstab` in case the NFS server is not live at boot.
## Directories
### `/bin`
- `/bin` contains executable binaries, essential commands used to boot the system or in single-user mode, and essential commands required by all system users, such as **cat**, **cp**, **ls**, **mv**, **ps**, and **rm**.
- `/sbin` contains essential binaries related to system administration.
- Commands that are not essential for the system to boot or operate in single-user mode are placed in the `/usr/bin` and `usr/sbin` directories. But today, `/usr/bin` and `/bin` are actually just symbolically linked together, as for `/sbin`.
### `/proc`
- `/proc` is called **pseudo-filesystem** because they have no actual permanent presence anywhere on the disk.
- It contains virtual files (on memory) that permit viewing constantly changing kernel data.
### `/dev`
- It contains **device nodes**, a type of pseudo-file used by most hardware and software devices, except for network devices, which is:
	- Empty on the disk partition when it is not mounted.
	- Contains entries which are created by the **udev** system, which creates and manages device nodes on Linux, creating them dynamically when devices are found.
### `/var`
- It contains files that are expected to change in size and content as the system is running - variable.
	- System log files: `/var/log`
	- Packages and database files: `/var/lib`
	- Temporary files: `/var/tmp`
- It may be put on its own filesystem so that growth of the files can be accommodated and any exploding file sizes do not fatally affect the system.
### `/etc`
- Used for system configuration files.
- It contains no binary programs, although there are some executable scripts.
### `/boot`
- It contains few essential files needed to boot the system.
- For every alternative kernel installed on the system there are 4 files, each of theses files has a kernel version appended to its name:
	- `vmlinuz`: The compressed Linux kernel, required for booting.
	- `initramfs`: The initial ram filesystem, required for booting, sometimes called initrd, not initramfs.
	- `config`: The kernel configuration file, only used for debugging and bookkeeping.
	- `System.map`: Kernel symbol table, only used for debugging.
- The Rand Unified Bootloader (GRUB) files are also found under the `/boot` directory.
### `/lib`
- `/lib` contains 32-bit libraries for the essential programs in `/bin` and `/sbin`.
- On some Linux distributions there exists a `/lib64` directory containing 64-bit libraries.
### Removable media
- Historically it was mounted under the `/media` directory, modern Linux distributions place these mount points under the `/run` directory.
- The `/mnt` directory has been used since the early days of UNIX for temporarily mounting filesystems. These can be those on removable media, but more often might be network filesystems, which are not normally mounted. Or these can be temporary partitions, or so-called **loopback** filesystems, which are files which pretend to be partitions.
## Working with files
- To compare files, use `diff`.
- To patch a file, use `patch`
### Back up data
- Use `cp` or `rsync` to back up data.
- `rsync` is more efficient, because it checks if the file being copied already exists. If the file exists and there is no change in size or modification time, `rsync` will avoid an unnecessary copy and save time.
```ad-note
rsync can be very dewstructive. Accidental misuse can do a lot of harm to data and programs, by inadvertently copying changes to where they are not wanted.
```
### Compress data
- Common utilities:
	- `gzip`
	- `bzip2`: Produces files significantly smaller than those produced by `gzip`
	- `xz`: The most space-efficient compression utility used in Linux
	- `zip`
- `tar` (tap archive) was used to archive files to a magnetic tape, allows you to create or extract files from an archive file, often called a `tarbal`.
### `dd` (disk-to-disk copying)
- The `dd` program is very useful for making copies of raw disk space.
- An exact copy of the first disk device is created on the second disk device. It will delete everything that previously existed on the second disk