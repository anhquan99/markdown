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