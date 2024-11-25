# Linux documentation
![[Pasted image 20241125070322.png]]
- Sources:
	- The man pages (short for manual pages)
	- GNU Info
	- `help` command and `--help` option
	- Other resources: desktop help system, package documentation, online resources.
## man pages
- The man program searches, formats, and displays the information contained in the man page system.
- `man -f` generates the same result as `whatis`.
- `man -k` generates the same results as `apropos`.
- The man pages are divided into chapters numbered 1 through 9.
## The GNU info system
- This is the GNU project's standard documentation format, which it prefers as an alternative to **man**. The Info System is basically free-form, and supports linked subsections.
- While the info interface may seem to be rather outdated compared to modern help systems (even man), it is often the only easy source to get more complete information.
- Command: `info`.
- The topic in an info page is called node.

| Key | Function                      |
| --- | ----------------------------- |
| n   | Go to next node               |
| p   | Go to the previous node       |
| u   | Move one node up in the index |
## help
### `--help` option
- Use either `--help` or `-h`.
- The `--help` option is useful as a quick reference, and it displays information faster than the `man` or `info` pages.
### `help` command
- When run within a `bash` command shell, some popular commands (such as `echo` and `cd`) actually run especially built-in `bash` versions of the commands rather than the usual binaries found on the file system, say under `/bin` or `/usr/bin`.