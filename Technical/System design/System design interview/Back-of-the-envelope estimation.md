# Back-of-the-envelope estimation
## Power of 2
| Power | Approximate value | Full name  | Short name |
| ----- | ----------------- | ---------- | ---------- |
| 10    | 1 Thousand        | 1 Kilobyte | 1 KB       |
| 20    | 1 Million         | 1 Megabyte | 1 MB       |
| 30    | 1 Billion         | 1 Gigabyte | 1 GB       |
| 40    | 1 Trillion        | 1 Terabyte | 1 TB       |
| 50    | 1 Quadrillion     | 1 Petabyte | 1 PB       |
## Common latency numbers

| Operation                               | Time                  |
| --------------------------------------- | --------------------- |
| L1 cache reference                      | 0.5 ns                |
| Branch mispredict                       | 5 ns                  |
| L2 cache reference                      | 7 ns                  |
| Mutex lock/unlock                       | 100 ns                |
| main memory reference                   | 100 ns                |
| Compress 1K bytes with Zippy            | 10,000 ns             |
| Send 2K bytes over 1 Gbps network       | 20,000 ns             |
| Read 1 MB sequentially from memory      | 250,000 ns            |
| Round trip within the same datacenter   | 500,000 ns            |
| Disk seed                               | 10,000,000 ns = 10 ms |
| Read 1 MB sequentially from the network | 10,000,000 ns = 10 ms |
| Read 1 MB sequentially from disk        | 30,000,000 ns = 30 ms |
```ad-note
- ns = nanosecond
- ms = milisecond
- 1 ns = 10^(-9) seconds
- 1 ms = 10^(-3) seconds
```
### Conclusions
- Memory is fast but the disk is slow
- Avoid disk seeks if possible
- Simple compression algorithms are fast
- Compress data before sending it over the internet if possible
- Data centers are usually in different regions, and it takes time to send data between them
## Availability numbers
| Availability | Downtime per day | Downtime per year |
| ------------ | ---------------- | ----------------- |
| 99%          | 14.40 mins       | 3.65 days         |
| 99.9%        | 1.44 mins        | 8.77 hours        |
| 99.999%      | 8.64 sec         | 52.6 mins         |
| 99.9999%     | 864 millisec     | 5.26 mins         |
| 99.99999%    | 86.4 millsec     | 31.56 sec         |
## Tips
- Back-of-the-envelope estimation is all about the process. Solving the problem is more important than obtaining results. Interviewers may test your problem-solving skills. Here are a few tips to follow:
	- Rounding and approximation
	- Write down your assumptions
	- Label your units
	- Commonly asked back-of the-envelop estimations: QPS (query per second), peak QPS, cache number of servers, ...