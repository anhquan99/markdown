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
### MBR (Master Boot Record)
- It is used in the early days of MSDOS.
- In MBR, a disk my have up to 4 primary partitions. 1 of the primary partitions can be designated as an extended partition, which can be subdivided further into logical partitions with 15 partitions possible.
- If the system have SCSI, for example, `/dev/sda`, then `/dev/sda1` is the first primary partition and `/dev/sda2` is the second primary partition. If we created an extended partition `/dev/sda3`, it could be divided into logical partitions. All partitions greater than four are logical partitions (meaning contained within an extended partition). There can only be one extended partition, but it can be divided into numerous logical partitions.
### GPT (GUID Partition Table)
- GPT is on all modern system and is based on UEFI (Unified Extensible Firmware Interface).
- By default, it may have up to 128 primary partitions. When using the GPT scheme, there is no need for extended partitions. Partitions can be up to 233 TB in size (with MBR, the limit is just 2TB).
## Why partitions?
- Separation of user and application data from operating system files
- Sharing between operating systems and/or machines
- Security enhancement by imposing different quotas and permissions for different system parts
- Size concerns; keeping variable and volatile storage isolated from stable
- Performance enhancement of putting most frequently used data on faster storage media
- Swap space can be isolated from data and also used for hibernation storage.