# Graphical Desktop
- Loading the graphical desktop is 1 of the final step in the boot process of a Linux desktop.
- A service called the Display Manager keeps track of the displays being provided and load the graphical service (X Window System or Wayland). The display manager also handles graphical logins and starts the appropriate desktop environment after a user logs in.
  ![[Pasted image 20241110122220.png]]
- A desktop environment consists of a session manager, which starts and maintains the components of the graphical session, and the window manager, which controls the placement and movement of windows, windows title-bars, and controls.
    ![[Pasted image 20241110122340.png]]
- Suspend mode saves the current system state and allows you to resume your session more quickly while remaining on, but uses very little power in the sleeping state by keeping your system's applications, desktop, ... in system RAM but turning off all of the other hardware.
## Types
- `GNOME` - `gdm`
	- Standard utility - `gnome-tweaks` is used for users modify settings of the `GNOME`.
- `KDE` - `kdm`