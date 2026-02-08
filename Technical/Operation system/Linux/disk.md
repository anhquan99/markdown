# Disk
## Common disk types
- **SATA** (Serial Advanced Technology Attachment)
- **SCSI** (Small Computer Systems Interface)
- **SAS** (Serial Attached SCSI)
- **USB** (Universal Serial Bus)
- **SSD** (Solid State Drives)
- **IDE** and EIDE (Integrated Drive Electronics, Enhanced IDE): obsolete
## Disk Geometry
- Available with disks has physical moving parts.
```bash
sudo fdisk -l /dev/sdc | grep -i sector # see geometry
```

## Partition organization
- Disks are divided into partitions. A partition is a physically contiguous region on the disk.
- The Linux kernel interacts at a low level with disks through device nodes normally found in the **/dev** directory. Normally, device nodes are accessed only through the infrastructure of the kernel's Virtual File System; raw access through the device nodes is an extremely efficient way to destroy a filesystem.
### MBR (Master Boot Record)
- It is used in the early days of MSDOS.
- In MBR, a disk my have up to 4 primary partitions. 1 of the primary partitions can be designated as an extended partition, which can be subdivided further into logical partitions with 15 partitions possible.
- If the system have SCSI, for example, `/dev/sda`, then `/dev/sda1` is the first primary partition and `/dev/sda2` is the second primary partition. If we created an extended partition `/dev/sda3`, it could be divided into logical partitions. All partitions greater than four are logical partitions (meaning contained within an extended partition). There can only be one extended partition, but it can be divided into numerous logical partitions.
![[Pasted image 20250122065450.png]]
- **Partition Flag** - Status of physical drive (bit 7 set is for active or bootable, old MBRs only accept 80hex, 00hex means inactive, and 01hex–7Fhex stand for invalid)
- **Start CHS Address** - CHS(Cylinder-Head-Sector) address of first absolute sector in partition
- **Partition Type** - It is a byte value intended to specify the file system the partition contains and/or to flag special access methods used to access these partition
- **End CHS Address** - CHS(Cylinder-Head-Sector) address of last absolute sector in partition
- **Start LBA** - LBA(Logical Block Addressing) of first absolute sector in the partition
- **Number of sectors** - Total number of sectors present in this partition
#### Limitation
- MBR limits the maximum addressable storage space of a disk to 2 TiB (2^32 × 512 bytes) for disk with 512 bytes sectors.
- MBR also only supports up to four primary partitions—if you want more, you have to make one of your primary partitions an “extended partition” and create logical partitions inside.
- The partitioning and boot data is stored in one place. If this data is overwritten or corrupted, you’re in trouble.
### GPT (GUID Partition Table)
- GPT is on all modern system and is based on UEFI (Unified Extensible Firmware Interface).
- By default, it may have up to 128 primary partitions. When using the GPT scheme, there is no need for extended partitions. Partitions can be up to 233 TB in size (with MBR, the limit is just 2TB).
- There are two copies of the GPT header, at the beginning and at the end of the disk, describing metadata:
	- List of usable blocks on disk
	- Number of partitions
	- Size of partition entries. Each partition entry has a minimum size of 128 bytes.
![[Pasted image 20250122070106.png]]
## Why partitions?
- Separation of user and application data from operating system files
- Sharing between operating systems and/or machines
- Security enhancement by imposing different quotas and permissions for different system parts
- Size concerns; keeping variable and volatile storage isolated from stable
- Performance enhancement of putting most frequently used data on faster storage media
- Swap space can be isolated from data and also used for hibernation storage.
### Disk naming
- Example the machine has 3 disk:
```
sda (first disk with a postfix)
  ├─sda1 (first partition of first disk)
  └─sda2 (second partition of first disk)
sdb (second disk with b postfix)
  ├─sdb1
  ├─sdb2
  └─sdb3
sdc (third disk with c postfix)
  └─sdc1
```
- The disk naming is placed in order of the disk placement. If you swap the order of the disk then the name remain the same but the disk behind is swapped.
	- Example: at first, sda is disk 1 and sdb is disk 2. After swapping it, sda is disk 2 and sdb is disk 1.
### `blkid`
- A utility to locate block devices and report on their attributes.
- Only work on devices which contain data that is finger-printable.
### `lsblk`
- It presents block device information in a tree format.
### `fdisk`
- List of partitions on the block devices.
- The beginning of the has an empty space where it is not partitioned. It is a standard procedure to leave 1 MB free of unpartitioned space at the beginning.
	- This is done in case a special program called a bootloader needs to be installed in that area.
```shell
sudo fdisk --list {disk}
```
## `cfdisk`
- Used for partition a disk.
- To save all the change, remember to enter Write button.
```shell
sudo cfdisk {disk}
```
## Sizing up partitions
- Most Linux systems should use a minimum of 2 partitions:
	- **`root` is used for filesystem.**
	- **Swap is used as an extension of physical memory.** The usual recommendation is swap size should be equal to physical memory in size; sometimes twice that is recommended. However, the correct choice depends on the related issues of system use scenarios as well as hardware capabilities. Adding more and more swap will not necessarily help because at a certain point it becomes useless. One will need to either add more memory or re-evaluate the system setup.
## Backing up and restoring partition tables
### MBR
- `dd` can be used for converting and copying files.
- A simple typing error or misused option could destroy entire disk.
```bash
dd if=/dev/sda of=mbrbackup bs=512 count=1 # backup
sudo dd if=mbrbackup of=/dev/sda bs=512 count=1 # restore
```
### GPT
- `sgdisk` is used for GPT systems.
```bash
sudo sgdisk -p /dev/sda
```
## Performance monitoring
### `sysstat`
- `iostat`: I/O statistic
- `pidstat`: process ID statistic
- `tps`: transfer per second
```shell
# show usage of storage every 1 second
iostat 1
# device utilization
iostat -d
# human-readable
iostat -h
# show all partition
iostart -p ALL

# show process IO
pidstat -d
# human-readable
iostat -d --human

# check the mapping of the volume 
sudo dmsetup info /dev/dm-0
```
## Reference
- https://easylinuxji.blogspot.com/2018/12/what-is-disk-partitioning-disk.html