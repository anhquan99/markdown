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