# Logging
## Daemons
- Logging daemons are applications that collect, organize, and store logs.
- Most popular is `rsyslog` (Rocket Fast System for Log Processing)
- Logs are stored in the `/var/log` directory.
## Finding in log files
### Utils
- Using `grep`.
- If the log reaches its size limit, a process called **Log Rotation** kicks in to reorganize the log files:
	1. Rename the files: the current log is renamed with a post-fix number. Ex: `auth.log -> auth.log.1, auth.log.1 -> auth.log.2`.
	2. Delete the log that exceed the limit number of logs. This can be configured. Example the `rsyslog` can be configured in the `/etc/logrotate.d/rsyslog` with the configuration `rotate`.
	3. Compress the old log file.
### Following log files
- Use `tail -F /path/to/log`
### `journalctl`
- Modern Linux started using an additional way to keep track of logs.
- A System Journal Daemon is a bit smarter about how it collects data.
- `journalctl` helps us analyze log more efficiently.
- On some OS, the journaling daemon is configured to only keep logs for the current boot and only in memory. If the folder `/var/log/journal` exists, then journal daemon will store the log in the disk.
```shell
# journalctl {control}
journalctl /usr/bin/sudo # show log generated from sudo commmand, it need to find the location of the command

# journalctl -u {service}
journalctl -u sshd.service

journalctl -f # follow log

# look for log with serverity level
# journalctl -p <level>
# you can tab for list of priority
journalctl -p err

# multiple option combination
journalctl -p info -g '^b'

# look for log since time
journalctl -S 02:00 # since 2AM
# until
journalctl -S 01:00 -U 02:00 # since 1AM until 2AM
# with date and time
journalctl -S '2024-03-03 01:00:30'

# look for log in the current boot
journalctl -b 0
# last boot
journalctl -b -1
```
### `last`
- History of who logged into the system.
### `lastlog`
- Shows when user logged in the last time.