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
## Process
- The process of backup is followed:
	1. Archive
	2. Compress
	3. Backup
## Commands
### `tar`
- Stand for **T**ape **Ar**chive.
- Support archive and compress with or the tools like `gzip, bzip2, xz`.
```shell
# display content of a tar file
tar --list --file file.tar
tar -tf file.tar
tar tf file.tar

# archive a file
tar --create --file file.tar archive_file
# append to existing tar file
tar --append --file file.tar achive_file
# archive a folder
tar --create --file file.tar folder/

# extract to specific dir
tar --extract --file archive.tar --directory /tmp/
tar xf archive.tar -C /tmp/ # same as above

# compress with other compress tools
tar --create --[tool-name] file-name.tar.[tool-compressed-extension]
# tar can auto regconize tool that the file is compressed by, use --autocompress option
tar --create --autocompress --file archive.tar.gz file
tar caf archive.xz file # same as above
tar --extract --file archive.tar.gz # auto regcongize the xz
tar xf archive.tar.gz file # same as above
```
### `gzip`
- Use `-k` option to prevent deletion.
- `gzip` cannot compress folder, but with the help of `tar` it can. This can apply for `bzip2` and `xz`.
```shell
# to compress file and delete original file
gzip file # result file.gz

# to decompress file and delete compressed file
gunzip file.gz
gzip --decompress file.gz

# compress with tar
tar --create --gzip --file achive.tar.gz file
tar czf achive.tar.gz file
```
### `bzip2`
- Use `-k` option to prevent deletion.
```shell
# to compress file and delete original file
bzip2 file # result file.bz2

# to decompress file and delete compressed file
bunzip file.bz2
bzip2 --decompress file.bz2

# compress folder with tar
tar --create --bzip2 --file achive.tar.bz2 file
tar cjf achive.tar.bz2 file
```
### `xz`
- Use `-k` option to prevent deletion.
```shell
# to compress file and delete original file
xz file # result file.xz

# to decompress file and delete compressed file
unxz file.xz
xz --decompress file.xz

# compress folder with tar
tar --create --xz --file achive.tar.xz file
tar CJf achive.tar.xz file
```
### `zip`
- Support compress file and folder.
```shell
zip -r achive.zip folder

unzip archive.zip
```
### `rsync`
- **R**emote **Syn**chronization
- Required remote server must have `ssh` daemon.
```shell
resync -a source destination
# -a for all file and folder
```
### `dd`
- Disk imaging
```shell
sudo dd if=/dev/vda of=diskimage.raw bs=1M status=process
# if is for disk partition path
# of is for output
# bs is for block size
# status is to tell dd show us the process

sudo dd if=diskimage.raw of=dev/vda bs=1M status=process # resver disk imaging
```
