# Boot Process
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
*The only purpose of an `initramfs` is to mount the root filesystem. The `initramfs` is a complete set of directories that you would find on a normal root filesystem. It is bundled into a single `cpio` archive and compressed with one of several compression algorithms.*
- The `initramfs` filesystem image contains programs and binary files that perform all actions needed to mount the proper root filesystem, that will be used, and loading the device drivers for mass storage controllers, by taking advantage of the `udev` system (for user device), which is responsible for figuring out which devices are present, locating the device drivers they need to operate properly, and loading them. After the root filesystem has been found, it is checked for errors and mounted.
- The **mount** program instructs the operating system that a filesystem is ready for use and associates it with a particular point in the overall hierarchy of the filesystem (the **mount point**). If this is successful, the `initramfs` is cleared from RAM, and the **init** program on the root filesystem (**/sbin/init**) is executed.

- init handles the mounting and pivoting over to the final real root filesystem. If special hardware drivers are needed before the mass storage can be accessed, they must be in the `initramfs` image.
![[Pasted image 20241030075937.png]]
# Linux Kernel
- The boot loader loads both the kernel and an initial RAM-based file system (`initramfs`) into memory, so it can be used directly by the kernel.
- When the kernel is loaded in RAM, it immediately initializes and configures the computer’s memory and also configures all the hardware attached to the system. This includes all processors, I/O subsystems, storage devices, etc. The kernel also loads some necessary user space applications.
# `/sbin/init` and Services
- Once the kernel has set up all its hardware and mounted the root filesystem, the kernel run `/sbin/init`. This then becomes the initial process, which then starts other processes to get the system running. Most other processes on the system trace their origin ultimately to `init`; exceptions include the so-called kernel processes. These are started by the kernel directly, and their job is to manage internal operating system detail.
- `init` is responsible for:
	- Starting the system
	- Act when necessary as a manager for all non-kernel processes
	- Cleans up after upon completion
	- Restarts user login services as needed when login, logout and same for other background system services
- Traditionally, this process startup was done using `SysVinit` which pass the system through a sequence of `runlevels` containing collections of scripts that start and stop services. However, all major distributions have moved away from this method of system initialization because it did not take advantage of the parallel processing.
- There are 2 main startup alternatives:
	- `Upstart`
	- `systemd`
# `systemd`
- `systemd` is called systemd units. A collection of tools, components, and applications that responsible for system initialization and monitoring the system as a whole to ensure smooth operation. 
- `systemd` start up faster than those with earlier `init` methods because it replaces a serialized set of steps with aggressive parallelization techniques, which permits multiple services to be initiated simultaneously.
- `/sbin/init` points to `/lib/systemd/systemd`.
- System unit files: `/etc/systemd/system` and `/lib/systemd/system`.
- User unit files: `/etc/systemd/user` and `~/.config/systemd/user`.
- Units:
	- Service
	- Socket
	- Device
	- Timerw
- If the status of the service is enabled then service is started when the system boots. If it is disabled then the service can still run manually, but it will not start when the system boots.
- When changing the config of a service, and it is running. The old config still applies to the running service. You can `restart` the service, but it will make disruption to other people who are using it, to prevent this you can use `reload` to gracefully restart the service.
- 
# Filesystems
## Analogy
- A single page is just a small piece of data (a "block"). To create a **book (a File)**, the **Filesystem (the Librarian)** gathers several related pages together, binds them in a specific order, and gives them a title.
- To make the analogy complete, look at it this way:
	- **The Data (Pages):** Individual pieces of information.
	- **The File (The Book):** A collection of those pages that make sense together (like a photo or a document).
	- **The Folder (The Section):** The "Science" or "History" section where multiple books are kept together.
	- **The Filesystem (The Librarian & Index):** The system that knows which book is on which shelf and how to find it using the title.
## Structure
- Linux uses the `/` character to separate paths and does not have drive letters.
  ![[Pasted image 20241109161937.png]]
  # Choosing a Linux Distro
  ![[Pasted image 20241109162113.png]]
## Single user mode
- In this mode, system boots to `runlevel` 1. Because single user mode automatically tries to mount your filesystem, you cannot use it when your root filesystem cannot be mounted successfully, or if the init configuration is corrupted.
- In single user mode:
	- init is started
	- Services are not started
	- Network is not activated
	- All possible filesystems are mounted
	- root access is granted without a password
	- A system maintenance command line shell is launched
- To boot into single user mode, you use the same method as described for emergency mode with one exception, replace the keyword **emergency** with the keyword **single**.
# Questions worth thinking about before deciding on a distribution include:
  - What is the main function of the system (server or desktop)?
  - What types of packages are important to the organization? For example, web server, word processing, etc.
  - How much storage space is required, and how much is available? For example, when installing Linux on an embedded device, space is usually constrained.
  - How often are packages updated?
  - How long is the support cycle for each release? For example, LTS releases have long-term support.
  - Do you need kernel customization from the vendor or a third party?
  - What hardware are you running on? For example, it might be **X86**, **RISC-V**, **ARM**, **PPC**, etc.
  - Do you need long-term stability? Or can you accept (or need) a more volatile cutting-edge system running the latest software versions?
## Commands
### `systemctl`
- Stand for **System** **C**on**t**ro**l**.
```sh
# reboot
systemctl reboot
systemctl reboot --force # force reboot
systemctl reboot --force --force  # like pressing restart button

# shutdown
systemctl poweroff
shutdown 02:00 # scheduled shutdown
shutdown +15 # shutdown after 15 mins
shutdown -r +1 # shutdown and reboot
shutdown -r +1 'wall message' # shutdown with message
```
- `systemctl get-default` get default target of `systemd`, which mean the program is called when the system is booted. Targets:
	- `graphical.target`: graphical interface
	- `emergency.target`: load up as few programs as possible
	- `rescue.target`: load more programs than emergency but fewer than graphical
```shell
systemctl set-default multi-user.target # set boot with all of its daemons but the graphical interface is skipped
# to boot back with the graphical interface
systemctl isolate graphical.target

# get service file
systemctl cat ssh.service
# edit service
sudo systemctl edit --full ssh.service
#reset to factory
sudo systemctl revert ssh.service
# check the service is enable
systemctl is-enabled ssh.service
# enable and auto start the service, also apply to disable
sudo systemctl enable --now ssh.service
# disable service from starting even manual
sudo systemctl mask ssh.service
# list service
sudo systemctl list-units --type service --all

# list dependencies
systemctl list-dependencies 
```