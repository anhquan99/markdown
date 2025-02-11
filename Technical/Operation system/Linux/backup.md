# Backup
## Methods
- **Full**: backup all files on the system.
- **Incremental**: backup for all files that have changed since the last incremental or full backup.
- **Differential**: backup for all files that have changed since the last full backup.
- **Multiple level incremental**: backup for all files that changed since the previous backup at the same or a previous level.
- **User**: backup for user files.
## Utilities
- `cpio` and `tar`: create and extract archives of files.
- `gzip, bzip or xz`.
- `dd`: transfer raw data between media.
- `dump and restore`: ancient utilities.
- `mt`: used for querying and positioning tapes before performing backups and restores.
- `rsync`: remote sync - used to transfer files across a network.
## Programs
- Amanda (Advanced Maryland Automatic)
- Bacula
- Clonezilla