# Input/Output
## Problem
- A system can be considered as I/O-bound when the CPU is found sitting idle waiting for I/O to complete, or the network is waiting to clear buffers.
- If memory buffers that being used for reading and writing fill up, it may appear that memory is the problem. The problem is that buffets are not filling up or empty out fast enough.
- Network transfers may be waiting for I/O to complete and cause network throughput to suffer.
## Monitoring and tuning
### `iostat`
- A utility for monitoring I/O device activity on the system, which generates reports with information.
### `iotop`
- Must be run as root.
- It displays a table of current I/O usage and update periodically.
- In the column `PRIO`, `be` stands for *best effort* and `rt` stands for *real time*.