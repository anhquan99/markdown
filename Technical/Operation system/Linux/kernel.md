# Kernel
- The kernel is the essential central component that connects the hardware to the software and manages system resources, such as memory and CPU time allocation among competing applications and services. It handles all connected devices using device drivers, and makes the devices available for operating system use.
## Responsibilities
- System initialization and boot up
- Process scheduling
- Memory management
- Controlling access to hardware
- I/O (Input/Output) between applications and storage devices
- Implementation of local and network filesystems
- Security control, both locally (such as filesystem permissions) and over the network
- Networking control
## Command line
- Can be found in the `grub.cfg` file in a subdirectory under `boot`, such as `/boot/grub`, or in a place like `/boot/efi/EFI/centos/grub.cfg`.
- Everything after the `vmlinuz` file specified is an option. Any options not understood by the kernel will be passed to init (pid = 1), the first user process to be run on the system.
## Boot process failures
### No bootloader screen
- Check for GRUB misconfiguration or a corrupt boot sector. Bootloader re-install needed?
### Kernel fails to load
- Most likely a misconfigured or corrupt kernel, or incorrect kernel boot parameters in the GRUB configuration file.
- You can re-install the kernel, or enter into the interactive GRUB menu at boot and use very minimal command line parameters and try to fix that way, or you can try booting into rescue image, depending on which.
### Kernel loads but fails to mount the root filesystem
- Misconfigured GRUB configuration file
- Misconfigured `/etc/fstab`
- No support for the root filesystem type built into the kernel or in the `initramfs` image
### Failure during the init process
- It can be many things, look closely at the messages that are displayed before things stop.
- Look out for corrupted filesystems, errors in startup scripts, etc.
- Try booting into a lower run level, such as 3 (no graphics) or 1 (single user mode).
## `sysctl`
- The `sysctl` interface can be used to read and tune kernel parameters at runtime.
- The value of the `sysctl` is corresponding to a particular pseudofile residing under `/proc/sys`.
- If settings are placed in the `/etc/sysctl.conf` file, these can be fixed at boot time.
## Kernel modules
- The Linux kernel makes extensive use of modules, which contain important software that can be dynamically loaded and unloaded as needed after the system starts.
- Kernel modules are located in `/lib/modules/$(uname -r)`and can be compiled for specific kernel versions.
- Kernel remains a ***monolithic***, not ***microkernel***.
- All files in the `/etc/modprobe.d` subdirectory tree which end with the `.conf` extension are scanned when modules are loaded and unloaded using `modprobe`.
### Utilities
- `lsmod`: list loaded modules.
- `insmod`: directly load modules.
- `rmmod`: directly remove modules.
- `modprobe`: load or unload module, using a pre-built module database with dependency and location information.
- `depmod`: rebuild the module dependency database.
- `modinfo`: display information about a module.
### Considerations
- Modules are usually loaded with `modprobe`, not `insomod`.
- Modules loads with non-acceptable open source licenses mark the kernel as tainted.
- There are some important things to keep in mind when loading and unloading modules:
	- It is impossible to unload a module being used by one or more other modules, which one can ascertain from the `lsmod` listing.
	- It is impossible to unload a module that is being used by one or more processes, which can also be seen from the `lsmod` listing. However, there are modules which do not keep track of this reference count, such as network device driver modules, as it would make it too difficult to temporarily replace a module without shutting down and restarting much of the whole network stack.
	- When a module is loaded with `modprobe`, the system will automatically load any other modules that need to be loaded first.
	- When a module is unloaded with `modprobe -r`, the system will automatically unload any other modules being used by the module, if they are not being simultaneously used by any other loaded modules.