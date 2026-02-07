# Memory Management
- Changing memory parameters can have a large effect on I/O performance, and changing I/O parameters can have an equally large converse effect on the virtual memory sub-system.
- Memory monitoring utilities:

|Utility|Purpose|Package|
|---|---|---|
|**free**|Brief summary of memory usage|**procps**|
|**vmstat**|Detailed virtual memory statistics and block I/O, dynamically updated|**procps**|
|**pmap**|Process memory map|**procps**|
## `/proc/meminfo`
- Contains a wealth of information about how memory is being used.
## `/proc/sys/vm`
- Contains many tunable knobs to control the Virtual Memory system. Changes in this directory is depending on the kernel version. Almost all of the entries are writeable (by root).
- Best practice is to adjust 1 thing at a time and look for effects. The primary task are:
	- Controlling flushing parameters; i.e., how many pages are allowed to be dirty and how often they are flushed out to disk
	- Controlling swap behavior; i.e., how much pages that reflect file contents are allowed to remain in memory, as opposed to those that need to be swapped out as they have no other backing store
	- Controlling how much memory overcommission is allowed, since many programs never need the full amount of memory they request, particularly because of copy on write (COW) techniques
## `vmstat`
- Is a multipurpose tool that displays information about memory, paging, I/O, processor activity and processes.
## Swap
- Linux employs a virtual memory system, which allows it function more memory than it does. This kind of memory overcomisstion functions in 2 ways:
	- Many programs do not actually use all the memory they are given permission to use. Sometimes, this is because child processes inherit a copy of the parent's memory regions utilizing a **COW (Copy On Write)** technique, in which the child only obtains a unique copy (on a page-by-page basis) when there is a change.
	- When memory pressure becomes important, less active memory regions may be swapped out to disk, to be recalled only when needed again.
- The recommended swap size if the total RAM on the system.
- The commands involving swap are:
	- `mkswap`: format swap partitions or files
	- `swapon`: activate swap partitions or files
	- `swapoff`: deactivate swap partitions or files
- It is pointless to swap out file caching contents because it will be moved to disk again. Instead, dirty pages (memory containing updated file contents that no longer reflect the stored data) are flushed out to disk.
- It is also worth pointing out that in Linux, memory used by the kernel itself, as opposed to application memory, is _never swapped out_, in distinction to some other operating systems.
```shell
# show swap partition
swapon --show

# set partition as swap
sudo mkswap {partition}
sudo swapon --verbose {partition}

# stop using partition as swap
sudo swapoff {partition}
```
### Using a file as swap
- An alternative to swap partitions.
```shell
# create a swap file
# the dd command is used for copying and converting data at a low level. General syntax dd if=[input file] of=[output file] [options]
# the /dev/zero a special pseudo-device file that acts as an endless source of null characters (binary zeros, 0x00) when read from
# the command below write 1 MB block to file with 2048 times
sudo dd if=/dev/zero of=/swap bs=1M count=2048 status=progress

sudo chmod 600 /swap

sudo mkswap /swap
```
## OOM Killer
### Common solutions
- The simplest way to deal with memory pressure would be to permit memory allocations to succeed as long as free memory is available and then fail when all memory is exhausted.
- The second simplest way is to use swap space on disk to push some of the resident memory out of core; in this case, the total available memory (at least in theory) is the actual RAM plus the size of the swap space. The hard part of this is to figure out which pages of memory to swap out when pressure demands. In this approach, once the swap space itself is filled, requests for new memory must fail.
### Linux solution
- Linux permits the system to overcommit memory, so that it can grant memory requests that exceed the size of RAM plus swap. While this might seem foolhardy, many (if not most) processes do not use all requested memory.
- The kernel permits overcommission of memory, but only for pages dedicated to user processes; pages used within the kernel are not swappable and are always allocated at request time.
- Which processes are terminated is selected by the OOM (Out of Memory) killer.
### OOM Killer Algorithms
- Set value of `/proc/sys/vm/overcommit_memory`:
	- **0 (default)**: Permit overcommission, but refuse obvious overcommits, and give root users somewhat more memory allocation than normal users.
	- **1**: All memory requests are allowed to overcommit.
	- **2**: Turn off overcommission. Memory requests will fail when the total memory commit reaches the size of the swap space plus a configurable percentage (50 by default) of RAM. This factor is modified changing `/proc/sys/vm/overcommit_ ratio`.
- Process selection depends on a **badness** value, which can be read from `/proc/[pid]/oom_score` for each process.
- Adjustments can be made to a process’s `oom_adj_score` in the same directory for each task.
## Command
### `free`
- The column free is truly empty space.
- The column available is empty space and space being used for "speed-up" tasks that can be cleared instantly.
### `uptime`
- Load average:
	- The first number is the load average for the last 1 minute.
	- The first number is the load average for the last 5 minute.
	- The first number is the load average for the last 15 minute.
```shell
ubuntu:~$ uptime
 08:49:58 up 10 min,  0 user,  load average: 0.00, 0.00, 0.00
```
### `lscp`
- Show details of CPU.
### `lspci`
- Show detail of hardware of the system.