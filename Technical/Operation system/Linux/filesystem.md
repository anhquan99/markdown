# Filesystem
- Filesystems create a usable format on a physical partition.
- Files and their names are an abstraction camouflaging the physical I/O layer. Directly writing to disk from the command line (ignoring the filesystem layer) is very dangerous and is usually only done by low-level operating system software and not by user applications. An exception is some very high-end software such as enterprise data bases that do such **cmd** access to skip filesystem-related latency.
- Multiple filesystems may be (usually are) merged together into a single tree structure. Linux uses a virtual filesystem layer (VFS) to communicate with the filesystem software.
- Local filesystems generally reside within a disk partition which can be a physical partition on a disk, or a logical partition controlled by a Logical Volume Manager (LVM).
### Types
- Absolute path: starts with a root directory and then the sub directory
- Current / Working directory
	- `pwd`: **p**rint **w**orking **d**irectory
	- `cd`: **c**hange **d**irectory
## Inodes
- A file might have blocks of data scattered all over the disk, but the inodes remember where all data are stored.
- The file points to the inode, and the inode points to all the blocks of data that we require. And the inode is hard linked to the file.
- An inode is a data structure on disk that describes and stores file attributes, including its location.
- The inode is used by the operating system to keep track of properties such as location, file attributes (permissions, ownership, etc.), access time and other items.
- Inodes describe and store information about a file, including:
	- Permissions​
	- User and group ownership​
	- Size​
	- Timestamps (nanosecond)
![[Pasted image 20250121200334.png]]
```ad-note
Filenames are not stored in the inode; they are stored in the direcroty.
```
## Hard and soft links
- Each association of a directory file contents and an inode is known a link.
### Hard links
- Hard links point to an inode. They are made by using `In` without an option.
- All hard linked files have to be on the same filesystem.
- Changing the content of a hard linked file in 1 place may not change it in other places.
- If there is no link to the inode, it will be deleted.
#### Limitations
- Only hard link to files, not folders.
- Only hard link to files on the same filesystem.
#### Considerations
- Make sure to have the proper permissions to create the link file at the destination.
- Make sure all users involved have the required permissions to access that file.
### Soft links
- Soft (or symbolic) links point to a file name which has an associated inode. Another way to understand this is soft link points to the path of a file.
- Soft linked files may be on different filesystems.
- If the target does not yet exist or is not yet mounted, it can be dangling.
- Permission on the soft link do not matter.
- You can soft link to files and folders.
- A soft link hard link to files on the different filesystem.
## Virtual Filesystem (VFS)
- When an application needs to access a file, it interacts with the VFS abstraction layer, which then translates the I/O system calls into specific code relevant to particular actual filesystem.
## Journaling filesystems
- Journaling filesystems recover from system crashes or ungraceful shutdowns with little or no corruption, and do so very rapidly. While this comes at the price of having some more operations to do, additional enhancements can more than offset the price.
- In a journaling filesystem, operations are grouped into transactions. A transaction must be completed without error, atomically; otherwise, the filesystem is not changed. A log file is maintained of transactions. When an error occurs, usually only the last transaction needs to be examined.
## Varieties
- A list of currently supported kernel filesystems is available at `/proc/filesystems`.
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
### ext4
#### Features
- Supports volumes up to 1 EB and file sizes up to 16 TB.
- Backwards compatible with ext3 and ext2.
- The allocated space is usually guaranteed and contiguous. It also uses a performance technique called allocate-on-flush (delays block allocation until it writes data to disk). ext4 breaks the 32,000 subdirectory limit of ext3.
- ext4 uses checksums for the journal which improves reliability. This can also safely avoid a disk I/O wait during journalling, which results in a slight performance boost.
- Another feature is the use of improved timestamps. ext4 provides timestamps measured in nanoseconds.
#### Super block and block groups
- The **superblock** at the beginning contains information about the entire filesystem, which followed by block groups composed of sets of contiguous block:
	- Include administrative information
	- High redundancy of information in block groups
	- Other blocks store file data
- The block size is specified when the filesystem is created. It may be 512, 1K, 2K, 4K, 8K, etc. bytes, but not larger than a page of memory (4kB on x86).
- The default block size is 4 KB, which would create a block group of 128 MB.
![[Pasted image 20250204210232.png]]
- The first 1024 bytes are unused (to allow for boot sectors). The superblock will sstart the first block, except for block group 0. This is followed by the group descriptors and a number of GDT (Group Descriptor Table) blocks. These are followed by the data block bitmap, the inode bitmap, the inode table, and the data blocks.
- The block size is used  to set the maximum number of:
	- Blocks
	- Inodes
	- Superblocks
- You can use `dumpe2fs` program to get information about a particular partition.
- `tune2fs` can be used to change filesystem parameters.
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
## Logical volume management (LVM)
- It breaks up 1 virtual partition into multiple chucks, each of which can be on different partitions and/or disks.
- Advantage when using LVM is easy to change the size of the logical partitions and filesystems, to add more storage space, rearrange things.
- LVM impact performance. However, even on non-RAID system, if you use striping (splitting of data to more than 1 disk), you can achieve some parallelization improvements.
### Volume groups
- Partitions are converted to physical volumes and multiple physical volumes are grouped into volume groups.
- Logical volumes are allocated from volume groups:
	- Can be defined by the size or number of extents.
	- Filesystems are built on logical volumes.
	- Can be named anything.
![[Pasted image 20250209190338.png]]
#### Utilities
- Manipulate volume group. The name always starts with `vg`:
	- `vgcreate`: creates volume groups
	- `vgextend`: adds to volume groups
	- `vgreduce`: shrinks volume groups
- Manipulate physical partitions. The name starts with `pv`:
	- `pvcreate`: converts a partition to a physical volume
	- `pvdisplay`: shows the physical volumes being used
	- `pvmove`: Moves the data from one physical volume within the volume group to others; this might be required if a disk or partition is being removed for some reason. It would then be followed by:
	- `pvremove`: remove a partition from a physical volume
- `man lvm`: gives a full list of LVM utilities.
- Manipulate logical volumes. The name always starts with `lv`:
	- `lvcreate`: allocates logical volumes from within volume groups
	- `lvdisplay`: reports on available logical volumes
	- `lvresize`: resizes logical volumes
	- `lvremove`: remove logical volumes
- Create a new volume group:
	1. 1. Create partitions on disk drives (type **8e** in **fdisk**).
	2. Create physical volumes from the partitions.
	3. Create the volume group.
	4. Allocate logical volumes from the volume group.
	5. Format the logical volumes.
	6. Mount the logical volumes (also update the **/etc/fstab** file as needed).
## Network filesystem (NFS)
### Server
- NFS uses **daemons** (built-in networking and service processes in Linux).
- The `/etc/exports` contains the directions and permissions that a host is willing to share with other system over NFS.
- After modifying the `/etc/exports`, you can use `exportnfs -av` to notify Linux about the directories you are allowing to be remotely mounted using NFS, or you can use `systemctl restart nfs` but this is heavier, as it halts NFS for a short while before starting it up again.
### Client
- `/etc/fstab` to remote access the server filesystem.
- Use `mount <servername>:/<folder> <local-folder>` to mount the other filesystem to local folder.
- You may want to use `nofail` option in `fstab` in case the NFS server is not live at boot.
## Directories
### `/`
- `/` is not `/root`, the home directory of root, the superuser.
### `/bin`
- `/bin` contains executable binaries, essential commands used to boot the system or in single-user mode, and essential commands required by all system users, such as **cat**, **cp**, **ls**, **mv**, **ps**, and **rm**.
- `/sbin` contains essential binaries related to system administration.
- Commands that are not essential for the system to boot or operate in single-user mode are placed in the `/usr/bin` and `usr/sbin` directories. But today, `/usr/bin` and `/bin` are actually just symbolically linked together, as for `/sbin`.
### `/proc`
- `/proc` is called **pseudo-filesystem** because they have no actual permanent presence anywhere on the disk.
- It contains virtual files (on memory) that permit viewing constantly changing kernel data.
- Information and connections to processes and kernel.
- Configuration and administrative tasks.
- Contains files that don't exists, yet you can access these.
### `/dev`
- It contains **device nodes**, a type of pseudo-file used by most hardware and software devices, except for network devices, which is:
	- Empty on the disk partition when it is not mounted.
	- Contains entries which are created by the `udev` system, which creates and manages device nodes on Linux, creating them dynamically when devices are found.
![[Pasted image 20250105190120.png]]
### `/var`
- It contains files that are expected to change in size and content as the system is running - variable.
	- System log files: `/var/log`
	- Packages and database files: `/var/lib`
	- Temporary files: `/var/tmp`
- It may be put on its own filesystem so that growth of the files can be accommodated and any exploding file sizes do not fatally affect the system.
### `/etc`
- Used for system configuration files.
- It contains no binary programs, although there are some executable scripts.
- Subdirectories:
	- `/etc/skel`: contains skeleton files used to populate newly created home directories.
	- `/etc/systemd`: contains or points to configuration scripts for starting, stopping, and controlling system services when using `systemd`.
	- `/etc/init.d`: contains startup and shutdown scripts for when using System V initialization.
### `/boot`
- It contains few essential files needed to boot the system.
- For every alternative kernel installed on the system there are 4 files, each of theses files has a kernel version appended to its name:
	- `vmlinuz`: The compressed Linux kernel, required for booting.
	- `initramfs`: The initial ram filesystem, required for booting, sometimes called initrd, not initramfs.
	- `config`: The kernel configuration file, only used for debugging and bookkeeping.
	- `System.map`: Kernel symbol table, only used for debugging.
- The GRand Unified Bootloader (GRUB) files are also found under the `/boot` directory.
### `/lib`
- `/lib` contains 32-bit libraries for the essential programs in `/bin` and `/sbin`.
- On some Linux distributions there exists a `/lib64` directory containing 64-bit libraries.
- Recent distributions, `/lib` and `/usr/lib` (as well as `/lib64` and `/usr/lib64`) are symbolic link.
### `/mnt`
- System administrator can temporarily mount a filesystem when needed.
- Historically, `/mnt` was also used for the kinds of files which are now mounted under `/media` (or `/run/media`) in modern systems.
### `/opt`
- Designed for software packages that wish to keep all or most of their files in 1 isolated place, rather than scatter them all over the system in directories shared by other software.
### `/sys`
- This directory is the mount point for the `sysfs` pseudo-filesystem where all information resides only in memory.
- It contains information about devices and drivers, kernel modules, system configuration structures, ...
-  `sysfs` is used both to gather information about the system, and modify its behavior while running. In that sense, it resembles `/proc`, but it is younger than and has adhered to strict standards about what kind of entries it can contain.
### `srv`
- It contains site-specific data which is served by this system.
- This main purpose of specifying this is so that users may find the location of the data files for particular service, and so that services which require a single tree for readonly data, writable data and scripts (such as cgi scripts) can be reasonably placed.
### `/tmp`
- Used to store temporary files, and can be accessed by ant user or application.
- You must avoid creating large file on `/tmp`, it will occupy space in memory rather than disk, and it is easy to ham or crash the system through memory exhaustion.
### `/run`
- Used to store **transient files**, which are contain runtime information, which may need to be written early in system startup, and which do not need to be preserved when rebooting.
- Some existing locations, such as `/var/run` and `/var/lock`, will be now just symbolic links to directories under `/run`. Other locations, depending on distribution taste, may also just point to locations under `/run`.
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
## Commands
### `cd`
```shell
cd / # go to root directory
cd - # go to previous directory
cd # go to home directory
```
### `cp`
- Stand for **c**o**p**y, `cp [source] [destination]`
```shell
cp -r source dest # copy recursive
```
### `mv`
- **M**o**v**e, `mv source dest`
### `rm`
- **R**e**m**ove, `rm folder`
- Use `-r` to recursive remove child folder.
### `df`
- **D**isk **F**ree
- Use `-h` for human-readable size format.
- The folder `/tmpfs` is a virtual file system that only exist in the computer's memory.
- The folder `boot` is where the boot files are installed.
```
Example:
tmpfs           191M  988K  190M   1% /run
/dev/vda1        19G  5.2G   14G  29% /
tmpfs           952M   84K  952M   1% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/vda16      881M  117M  703M  15% /boot
/dev/vda15      105M  6.2M   99M   6% /boot/efi
```
### `du`
- **D**isk **U**sage.
- Options:
	- `s`: summerise
	- `h`: humman readable size format
### `xfs_repair`
- Verify the integrity of xfs filesystem.
```shell
sudo xfs_repair -v /dev/vda1
```
### `fsck.ext4`
- Verify the integrity of ext4 filesystem.
```shell
sudo fsck.ext4 -v -f -p /dev/vdb2
```