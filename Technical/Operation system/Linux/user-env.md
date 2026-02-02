# User accounts
- Linux is a multi-user operating system, meaning more than 1 user can log on at the same time.
## Startup files
- Files in the `/etc` directory define global settings for all users, while initialization files in the user's home directory can include and/or override the global settings.
- The standard prescription is that when you first login to Linux, `/etc/profile` is read and evaluated, after which the following files are searched (if they exist) in the listed order:
	1. `~/.bash_profile`
	2. `~/.bash_login`
	3. `~/.profile`
- Every time you create a new shell, or terminal window, you do not perform a full system login; only a file named `~/.bashrc` file is read and evaluated.
- Recent distribution sometimes do not even have `.bash_profile` or `.bash_login`.
![[Pasted image 20241231094149.png]]
## Users and groups
- All Linux users are assigned a unique user ID (`uid`), which is just an integer normal users start with a `uid` of 1000 or greater.
- Linux uses groups for organizing users. Group are collections of account with certain shared permissions, they are used to establish a set of users who have common interests for the purposes of access rights, privileges, and security considerations.
- Control of group membership is administered through the `/etc/group` file, which shows the list of groups and their members. By default, every user belongs to a default (primary) group, and all the members have the same level of access and privilege. Permissions on various files and directories can be modified at the group level, associated with names through the files `/etc/passwd and /etc/group`.
### Commands
- Use `useradd` to add new user:
	- Create an account for user
	- Create a primary group
	- Create home directory
		- Set a different home directory with option `--shell {shell-path}`
	- Set default shell is `/bin/bash`
		- Set a different shell with option `--shell {shell-path}`
	- Set ID for user with `--uid {id}`
	- Create a system account with option `--system --no-create-home`.
- `passwd` to change user password.
- Use `userdel` to remove user, this will leave the `/home/<user>` directory intact, the `-r` option helps remove the user directory while removing user account.
- Use `id` to show information of the current user.
- Use `groupadd` to add new group.
- Use `groupdel` to remove a group.
- Use `usermod` to modify user account.
	- Use `--move-home {directory}` to modify the directory of user or `usermod -d {directory} -m {user}`
	- Use `--login {new-username} {current-username}` to change username or `usermod -l {new-username} {current-username}`.
	- Use `--shell {new-shell} {user}` to change shell or `usermod -s {new-shell} {user}`.
	- Lock user account with `--lock` or `l`. Unlock use `--unlock` or `-U`.
	- Set expiration date of the account with `--expiredate {YYYY-MM-DD}` or `-e  {YYYY-MM-DD}` to disable user login.
	- To immediately set an account expired choose expired date in the past.
	- Remove expiration date set account to never expire.
- Set password expiration with `chage`
	- Option `--lastday` or `-d` is to set how long it will be expired. Un expire the password use value -1
	- Option `--maxdays` or `-M` same as above but has frequency. Set value -1 to remove expiration date.
```ad-note
- System account have ID range from 0 to 1000. System account is used for programs.

- User account have ID greater than 1000. User account is used for users.
```
### Root user
- `su` for switch or substitute user, which is used for switch user account.
- `sudo` configuration files are stored in the `/etc/sudoers` file and in the `/etc/sudoers.d/` directory.
## Env variables
- `$HOME`
- `$PATH` is an ordered list of directories which is scanned when a command is given to find the appropriate or script to run.
- `$SHELL` points to the user's default command shell.
- `$PS` (prompt statement) is used to customize your prompt string in your terminal windows to display the information you want.
- `$GLOBALENV` is global environment variables.
- `/etc/environment` is where global environment variable is set.
### Listing env variables
- `env`
- `set`
- `printenv`
## Command history
- To view the list of previously executed commands, you can type `history` at the command line, the history is saved in `~/.bash_history`.
- If you have multiple terminals open, the commands typed in each session are not saved until the session terminates.
### Using `!`
- All history substitutions start with `!`:
	- To start a history substitution: `!`
	- To refer to the last argument in a line: `!$`
	- To refer to the n-th command line: `!n`
	- To refer to the most recent command starting with `!string`
### Env variables for history
- `HISTFILE`: location of history file.
- `HISTFILESIZE`: max number of lines in the history file (default 500).
- `HISTSIZE`: max number of commands in the history file.
- `HISTIGNORE`: which command is ignored.
## Previous command
- Use `!!` to execute the previous command.
- Use `CTRL-R` to search previously used commands.

| Syntax    | Task                                                  |
| --------- | ----------------------------------------------------- |
| `!`       | Start a history substitution                          |
| `!$`      | Refer to the last argument in a line                  |
| `!n`      | Refer to the nth command line                         |
| `!string` | Refer to the most recent command starting with string |
## Keyboard shortcuts
| Shortcut | Task                                                                  |
| -------- | --------------------------------------------------------------------- |
| `CTRL-L` | Clear the screen                                                      |
| `CTRL-S` | Temporarily halt output to the terminal window                        |
| `CTRL-Q` | Resume output to the terminal window                                  |
| `CTRL-D` | Exits the current shell                                               |
| `CTRL-Z` | Puts the current process into suspended background and back to prompt |
| `CTRL-C` | Kills the current process                                             |
| `CTRL-H` | Works the same as backspace                                           |
| `CTRL-A` | Goes to the beginning of the line                                     |
| `CTRL-W` | Deletes the word before the cursor                                    |
| `CTRL-U` | Deletes from beginning of line to cursor position                     |
| `CTRL-E` | Goes to the end of the line                                           |
| `Tab`    | Auto-completes files, directories, and binaries                       |
## `sudo`
```bash
# execute as root
sudo command
# login as root
sudo --login
# or
sudo -i

# user don't have sudo priviledges but know sudo password
su -
# or 
su -l
# or 
su --login

# some system has root account locked but the sudo password is not so you can use -Does the user have permission to act like root?
sudo --login
# but can't do - What is the password for the root account?
su -

# lock root password
sudo passwd --lock root
# or
sudo passwd -l root
```