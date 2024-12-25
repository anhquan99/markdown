# Processes
- A process is an instance of one or more related tasks (threads) executing on your computer.
- A single command may actually start several processes simultaneously.
- Some processes are independent of each other and others are related. A failure of one process may or may not affect the others running on the system.
- Processes use many system resources, such as memory, CPU (central processing unit) cycles, and peripheral devices, such as network cards, hard drives, printers, and displays. 
- The operating system (especially the kernel) is responsible for allocating a proper share of these resources to each process and ensuring overall optimized system utilization.
## Types
| Process type          | Description                                                                                                                                                                                                                                                                                                                                                | Examples            |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- |
| Interactive processes | Need to be started by a user, either at a command line or graphical interface                                                                                                                                                                                                                                                                              | bash, firefox       |
| Batch processes       | Automatic process which are scheduled from and then disconnected from the terminal. These task are queued and work on a FIFO basis.                                                                                                                                                                                                                        | updatedb, Idconfig  |
| Daemons               | Server processes that run continuously. Many are launched during system startup and then wait for a user or system request indicating that their service is required.                                                                                                                                                                                      | httpd, sshd         |
| Threads               | Lightweight processes. These are tasks that run under the umbrella of a main process, sharing memory and other resources, but are scheduled and run by the system on an individual basis. An individual thread can end without terminating the whole process and a process an create new thread at any time. Many non-trivial programs are multi-threaded. | dconf-service       |
| Kernel threads        | Kernel tasks that users neither start nor terminate and have little control over. These may perform action like moving a thread from 1 CPU to another, or making sure IO operations to disk are completed.                                                                                                                                                 | kthreadd, migration |
## Scheduler
- A critical kernel function called the scheduler constantly shifts processes on and off the CPU, sharing time according to relative priority, how much time is needed and how much has already been granted to a task.
## States
- When a process is in a running state, it means it is either currently executing instructions on a CPU, or is waiting to be granted a share of time (a time slice) so it can be executed which is resided in a run queue. With the multiple CPUs, there is a run queue on each.
- Sleep state is when a process is waiting for something to happen before they can resume, like user input, etc ...
- Sometimes, a child process completes, but its parent process has not asked about its state, which is called **zombie** state. It is not really alive, but still shows up in the system's list of processes.
# Thread IDs
- Process ID (PID).
- Parent Process ID (PPID): if the parent dies, the PPID will refer to an adoptive parent, on modern kernels, this is `kthreadd` which has PPID=2.
- Thread ID (TID).
## Terminate a process
- Use command `kill -SIGKILL pid` or `kill -9 pid`.
- You can only kill your own processes.
## User and Groups IDs
- Many users can access a system simultaneously, and each user can run multiple processes. The OS identifies the user who starts the process by the Real User ID (RUID) assigned to the user or Real Group ID (RGID).
## Priorities
- The priority for a process can be set by specifying a **nice value**, or niceness. The lower the nice value, the higher the priority.
- The -20 is the highest priority and +19 is the lowest.
- You can assign a real-time priority to time-sensitive task.
## Load averages
- It is the average of the load number for a given period of time:
	- Actively running on a CPU
	- Considered runnable, but waiting on the run queue for a CPU to become available
	- Sleeping: Linux differs from other UNIX-like operating systems in that it includes the sleeping processes. Furthermore, it only includes so-called **uninterruptible** sleepers, those which cannot be awakened easily.
- Use `w, top or uptime` to view load averages.
- The load average displays utilization for a period of time using 3 numbers:
	- 1st: last minute 
	- 2nd: last 5 minutes
	- 3rd: last 15 minutes
## Background and foreground processes
- Linux supports background and foreground job processing. A job in this context is just a command launched from a terminal.
- Background process is a process running in the background where it is executed in a lower priority for smooth execution of the interactive task.
- Foreground process is executed directly in the foreground, other jobs will have to wait as this process is completed.
- You can put job in the background by suffixing `&`.
- You can use `CTRL-Z` to suspend a foreground job and `CTRL-C` to terminate it.
- `bg` command for suspend process in the background and `fg` command to run a background process in the foreground.
- `jobs` utility displays all jobs running in the background or the `jobs -l` for more detailed. The background jobs are connected to the terminal window, so if you log off, the `jobs` will not show the ones started from that window.
## Process status command 
### `ps`
- System V Style:
	- `-u` displays information of processes for a specified username
	- `-ef` displays all the processes in the system in full detail
	- `-eLf` displays 1 line of information for every thread (a process can contain multiple threads)
- BSD style:
	- `aux` displays all processes of all users
	- `axo` specify which attributes you want to view
### `pstree`
- Displays the processes running on the system in the form of a tree diagram.
### `top`
- Helps user monitor the system performance live over time with constant real-time updates (2 seconds by default).
- The first line displays: up time, number user logged on, load average.
- The second line displays: total number of processes, number of running, sleeping, stopped, and zombie process.
- The third line displays: indicates how the CPU time is being divided between the users (**us**) and the kernel (**sy**) by displaying the percentage of CPU time used for each.
- The fourth and fifth lines display: memory usage (physical memory - RAM and swap space)
- Each line in the process list displays:
	- **PID**
	- **USER**
	- Priority (**PR**) and nice value (**NI**)
	- Virtual (**VIRT**), physical (**RES**), and shared memory (**SHR**)
	- Status (**S**)
	- **%CPU** and **%MEM** used
	- Execution time (**TIME+**)
	- **COMMAND**
### Start processes in the future
- Use `at` to execute any non-interactive command at a specified time.
- `cron` is a time-based scheduling utility program.
- If the machine was powered off, scheduled jobs would not run, `anacorn` will run the necessary jobs in a controlled and staggered manner when the system is up and running.
- `sleep` command is used to delay or suspend a job.