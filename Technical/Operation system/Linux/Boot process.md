![[Pasted image 20241027233004.png|200]]
# BIOS
- Basic Input/Output System (BIOS) initializes the hardware, aka POST (Power On Self Test)
# MBR, EFI Partition, and Boot Loader
- After the POST stage, system control passes from the BIOS to the boot loader.
- The boot loader is usually stored on the 1 of the system's storage devices, either in the boot sector (for traditional BIOS/MBR systems) or the EFI partition (for more recent (Unified) Extensible Firmware Interface) or EFI/UEFI systems.
- At this stage, the machine does not access any mass storage media, the information date, time are loaded from CMOS.
- Boot loaders: 
	- GRUB (for GRand Unified Boot loader)
	- ISOLINUX (for booting from removable media)
	- DAS U-Boot (for booting on embedded devices/appliances)
- When booting, the boot loader responsible for loading the kernel image and the initial RAM disk or filesystem (which contains some critical files and device drivers needed to start the system) into memory.
![[Pasted image 20241028205918.png]]
# Boot loader stages
2 distinct stages:
- The first statge:
	- For system using BIOS/MBR, the boot loader resides at the first sector of the hard disk, known as the MBR (size 512 bytes). The boot loader examines the partition table and finds a bootable partition and find a bootable partition. It then searches for the second stage boot loader like GRUB, ... loads it into RAM.
	- For system using the EFI/UEFI method, UEFI firmware reads its Boot Manager data to determine which UEFI application is to be launched and from there (which disk and partition the EFI partition can be found). The firmware then launches the UEFI application, for example GRUB, as defined in the boot entry in the firmware's boot manager. This procedure is more complicated but more versatile than the older MBR.
- The second stage:  
	- Resides under `/boot`. A splash screen is displayed, which allows us to choose which OS and/or kernel to boot. After the OS and kernel are selected, the boot loader loads the kernel of the operating system into RAM and passes control to it.
	- Kernel are almost always compressed, so the first job they have is to uncompress themself. After this, it will check and analyze the system hardware and initialize any hardware device drivers build into the kernel.
# Initial RAM Disk
*The only purpose of an initramfs is to mount the root filesystem. The initramfs is a complete set of directories that you would find on a normal root filesystem. It is bundled into a single cpio archive and compressed with one of several compression algorithms.*
- The `initramfs` filesystem image contains programs and binary files that perform all actions needed to mount the proper root filesystem, that will be used, and loading the device drivers for mass storage controllers, by taking advantage of the `udev` system (for user device), which is responsible for figuring out which devices are present, locating the device drivers they need to operate properly, and loading them. After the root filesystem has been found, it is checked for errors and mounted.
- The **mount** program instructs the operating system that a filesystem is ready for use and associates it with a particular point in the overall hierarchy of the filesystem (the **mount point**). If this is successful, the `initramfs` is cleared from RAM, and the **init** program on the root filesystem (**/sbin/init**) is executed.

- init handles the mounting and pivoting over to the final real root filesystem. If special hardware drivers are needed before the mass storage can be accessed, they must be in the `initramfs` image.
![[Pasted image 20241030075937.png]]
# Linux Kernel
- The boot loader loads both the kernel and an initial RAM-based file system (`initramfs`) into memory, so it can be used directly by the kernel.
- When the kernel is loaded in RAM, it immediately initializes and configures the computer’s memory and also configures all the hardware attached to the system. This includes all processors, I/O subsystems, storage devices, etc. The kernel also loads some necessary user space applications.