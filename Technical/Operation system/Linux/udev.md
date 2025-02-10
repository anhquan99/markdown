# `Udev`
- Stand for **U**ser **Dev**ice management.
- It is an intelligent apparatus for discovering hardware and peripheral devices both during system boot, and later when they are connected to the system. Device Nodes are created automatically and then used by applications and operating system subsystems to communicate with and transfer data to and from devices. System administrators can control how `udev` operates and craft special `udev` rules to assure desired behavior results.
- `udev` handles loading and unloading device drivers with proper configurations, as needed:
	- Device naming
	- Device file and symlink creating
	- Setting file attributes
	- Taking needed actions
- When devices are added or removed from the system, `udev` receives a message from the kernel. It then parses the rules files in the `/etc/udev/rules.d` directory to see if any rules are there for the device added or removed.
## Components
- `udev` run as a daemon (either `udevd` or `systemd-udevd`) and monitors a netlink socket.
- When new devices are initialized or removed, the `uevent` kernel facility sends a message through the socket, which `udev` receives and takes appropriate action to create or remove device nodes of the right names and properties according to the rules.
- Components of `udev` are:
	- `libudev` lib which allows access to information about the devices
	- `udevd` or `systemd-udevd` daemon that manages the `/dev` directory
	- `udevadm` utility for control and diagnostics
![[Pasted image 20250210220921.png|300]]
## Device nodes
- Character and block devices has device nodes, network devices do not. These device nodes can be used by programs to communicate with devices through nodes using normal I/O methods.
- A device driver may use multiple device nodes. Device nodes are located in the `/dev` directory.
- The device nodes present in `/dev` are the primary or first interaction with the hardware on a machine.
- Some common devices are:
	- `/dev/sd*`: devices appearing as hard drives
	- `/dev/ttyS*`: serial ports, often used as consoles
	- `/dev/snd/*`: various sound devices
![[Pasted image 20250105190120.png]]
## Rule files
- `udev` rules files are located under `/etc/udev/rules.d/<rulename>.rules` and `/usr/lib/udev/rules.d/<rulename>/rules`.
- There are 2 parts defined on a single line:
	- The first part consists of 1 or more match pairs denoted by `==`. These try to match a device's attributes and/or characteristics to some value.
	- The second part consists of 1 or more assignment key-value pairs that assign a value to a name, such as file name, assignment, even file permissions, etc.
- If no matching rule is found, it uses the default device node name and other attributes.
- Rules files can be in 3 places, and the priority order is:
	1. `/etc/udev/rules.d`
	2. `/run/udev/rules.d`
	3. `/usr/lib/udev/rules.d`