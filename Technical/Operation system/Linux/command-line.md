# Command Line
## Input
- Command
- Options
- Arguments
## Turn off the Graphical Desktop
```shell
sudo systemctl stop gdm (or sudo telinit 3) // turn off
sudo systemctl start gdm (or sudo telinit 5) // turn on
```
## Basic operations
### Reboot and Shutdown
```shell
sudo shutdown -h HH:MM "message"
```
### Locate applications
```shell
which app_name
whereis app_name # looks for packages in a broader range
```
### Access directory
```shell
pwd
cd ~ or cd # go to home dir
cd ..
cd - # go to previous dir
tree # display a tree view of the filesystem
ls -a # list all file, including hidden files
pushd # push change the directory instead of cd, pushes your staring directory onto a list
popd # send you back to pushed directories, walking in reverse order
dirs # list pushed directories
```
- Paths:
	- Absolute path: begin with the root dir `/`.
	- Relative path: begin with the current working dir.
- The `cd` command remembers where you were last, and lets you get back there with `cd -`
## Links
- The `ln` utility is used to create hard links and (with the `-s` option) soft links, also known as symbolic links or symlinks.
- The linked file and the original file has the same [[command-line#Inode|inode number]].
### Hard links
- Hard links are useful and helps save space.
### Soft links
- Take no extra space on the filesystem (unless their names are very long).
- They are extremely convenient, as they can easily be modified to point to different places. An easy way to create a shortcut from your home directory to long pathnames is to create a symbolic link.
- Unlike hard links, soft links can point to objects even on different filesystems, partitions, and/or disks and other media, which may or may not be currently available or even exist. In the case where the link does not point to a currently available or existing object, you obtain a dangling link.
### Inode
- Denoted by the phrase "file serial number".
- An inode is an index node.
- It serves as a unique identifier for a specific piece of metadata on a give filesystem which describes what we think of as a file.
### Files
```shell
cat
tac # view file backwards
less # view file with pagination
tail # print last 10 lines of a file by default
head # print first 10 lines of a file by default
wc # word count
touch
mkdir
rmdir
mv
```
### Find files
#### locate
- `locate` utility program performs a search while taking advantage of a previously constructed database (`updatedb`) of files and directories.
- Most Linux system run `updatedb` automatically once a day.
#### find
- Finds recursive down the filesystem tree from any particular directory (or set of directories) and locates files that match specified conditions.
```shell
# find [/path/to/dir] [search parameters]
#      Go there        Find it
find /usr -name gcc # find files and directories
find /usr -type d -name gcc # find directory
find /usr -type f -name gcc # find file

find -name filename # search current directory

find -name filename # case sensitive
find -iname filename # case insensitive
find -name 'f*' # find file name start with f

# find -mmin [minutes] - find modified minute
find -mmin 5 # Exactly 5 minutes ago
find -mmin -5 # Less than 5 minutes ago
find -mmin +5 # More than 5 minutes ago
find -mtime 2 # 24 hour periods

find -ctime [minutes] # change time

# find -size [size] - find file with size
find -size 512k
find -size -512k
find -size +512k

find -name "f*" -size 512k # AND operator
find -name "f*" -o -size 512k # OR operator

find -not -name "f*" # NOT operator
find \! -name "f*" # alternate NOT operator

find -perm 664 # find files with exactly 664 permissions
find -perm -664 # find files with least 664 permissions
find -perm /664 # find files with any of these permissions
find -perm u=rw,g=rw,o=r # alternate way

# find and remove all files end with .swp 
# the {} is a placeholder that will be filled with all the file names that result from the expression, and the preceding command will be run on each one individually
# you have to end the command with either '**;'** (including the single-quotes) or **\;**. Both forms are fine.
find -name '*.swp' -exec rm {} ';'
```
- Modification = Create or Edit
- Modified Time != Change Time
	- Modified time: the time when the content was changed
	- Change time: the time when the metadata was changed
#### Wildcards and matching filenames
| Wildcard | Result                                             |
| -------- | -------------------------------------------------- |
| ?        | Matches any single character                       |
| *        | Matches any string of characters                   |
| [set]    | Matches any character in the set of characters     |
| [!set]   | Matches any character not in the set of characters |
### Modify the command line prompt
- The `PS1` variable is the character string that is displayed as the prompt on the command line.
## Standard file streams
| Name            | Symbolic name | Value | Example  |
| --------------- | ------------- | ----- | -------- |
| Standard input  | `stdin`       | 0     | Keyboard |
| Standard output | `stdout`      | 1     | Terminal |
| Standard error  | `stderr`      | 2     | Log file |
### I/O redirection
- Send the output, use `>`.
- Append to, use `>>`.
- Send input source to, use `<`.
- Send the `stderr`, use `2>`
- A special shorthand notation can send anything written to file descriptor 2 (`stderr`) to the same place as file descriptor 1 (`stdout`): `2>&1`.
### Pipes
- The UNIX/Linux philosophy is to have many simple and short programs (or commands) cooperate together to produce quite complex results, rather than have one complex program with many possible options and modes of operation. In order to accomplish this, extensive use of pipes is made. You can pipe the output of one command or program into another as its input.
```shell
command1 | command2 | command3
```
## Package management systems
![[Pasted image 20241124222644.png]]
- Package management systems operate on 2 distinct level:
	- Low level (`dpkg, rpm`): takes care of the details of unpacking individual packages, running scripts, getting the software installed correctly.
	- High level (`apt, dnf, zypper`): works with groups of packages, downloads packages from the vendor, and figures out dependencies.
- Dependency resolution is a particularly important feature of the high-level tool, as it handles the details of finding and installing each dependency for you.

| DEB                                                        | RPM                                                          |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| Deb files are used in Debian Linux based operating systems | Rpm files are used for Red Hat Linux based operating systems |
| Used in `dpkg` package manager                             | Used in `rpm` package manager                                |
