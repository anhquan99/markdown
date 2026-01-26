# Processes
- A process is an instance of one or more related tasks (threads) executing on your computer.
- A single command may actually start several processes simultaneously.
- Some processes are independent of each other and others are related. A failure of one process may or may not affect the others running on the system.
- Processes use many system resources, such as memory, CPU (central processing unit) cycles, and peripheral devices, such as network cards, hard drives, printers, and displays. 
- The operating system (especially the kernel) is responsible for allocating a proper share of these resources to each process and ensuring overall optimized system utilization.
```ad-note
A program may be composed of multiple simulataneous threads (multithreading), each of which is considered as its own process.
```
## Attributes
- **The program being executed**
- **Context (state)**: At any given moment, the process may take a snapshot of itself by trapping the state of its CPU registers, where it is executing in the program, what is in the process' memory, and other information.
- **Permissions**: Programs which are marked with an **s** execute bit have a different **effective** user `id` than their **real** user id. These programs are referred to as **setuid** programs. They run with the user-id of the user who owns the program, where a non-`setuid` program runs with the permissions of the user who starts it. `setuid` programs owned by root can be a security problem.
- **Associated resources**
## Process resource isolation
- When a process is started, it is isolated in its own user space to protect it from other processes. This promotes security and creates greater stability.
- Processes do not have direct access to hardware. Hardware is managed by the kernel, so a process must use **system calls** to indirectly access hardware. System calls are the fundamental interface between an application and the kernel.
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
- When a process is in a **running state**, it means it is either currently executing instructions on a CPU, or is waiting to be granted a share of time (a time slice) so it can be executed which is resided in a run queue. With the multiple CPUs, there is a run queue on each.
- A process may be **stopped**, meaning execution of instruction has been suspended. This state is commonly experienced when a programmer wants to examine the executing programs memory, CPU registers, etc. Once this done, the process may be resumed.
- **Sleep state** is when a process is waiting for something to happen before they can resume, like user input, etc ...
- Sometimes, a child process completes, but its parent process has not asked about its state, which is called **zombie** state. It is not really alive, but still shows up in the system's list of processes. Each zombie continues to take up space in O/S's process table. A Zombie process has all of its resources released except its process table entry.
# Thread IDs
- Process ID (**PID**).
- Parent Process ID (**PPID**): if the parent dies, the PPID will refer to an adoptive parent, on modern kernels, this is `kthreadd` which has PPID=2.
- Thread ID (**TID**).
- Process Group ID (**PGID**).
## Execution modes
- Instructions can be executed depends on the mode and is enforced at the hardware, not software, level.
- The mode is not a state of the system; it is a state of the processor, as in a multi-core or multi-CPU system each unit can be in its own individual state.
- In Intel parlance, user mode is also termed Ring 3, and system mode is termed Ring 0.
### User mode
- Processes executed in user mode has lesser privileges than the kernel mode.
- The process is isolated in its own user space to protect it from other processes, which promotes security and creates greater stability.
- The shared memory segments, a user process is not able to read or write into or from memory space of any other process.
- Even a process run by the root user or as a `setuid` program runs in user mode, except when jumping into a system call, and has only limited ability to access hardware.
![[Pasted image 20250119115505.png]]
### Kernel (system) mode
- In kernel mode, the CPU has full access to all hardware on the system.
- Application code never runs in kernel mode, only the system call itself which is kernel code. When the system call is complete, a return value is produced and the process returns to user mode with the inverse context switch.
## Create processes
- **forking**: the original parent process keeps running, while the new child process starts.
- **exec**: parent process terminates, and the child process inherits the process ID of the parent.
- Internal kernel processes take care of maintenance work, such as making sure buffers get flushed out to disk, that the load on different CPUs is balanced evenly, that device drivers handle work that has been queued up for them to do, etc. These processes often run as long as the system is running, sleeping except when they have something to do.
	- The first user process is called init and has **pid = 1**
	- Subsequent processes are forked from init or other running processes
	- Forking from a parent produces a child process, in every way a equal and a peer of the parent
	- If the parent dies, the child is "adopted" by init
	- There are also kernel-created processes; these have names surrounded by **[..]** in the output from **ps**
### Creating processes in a Command shell
- Steps:
	- A new process is created (forked from the user's login shell)
	- A wait system call puts the parent shell process to sleep
	- The command is loaded onto the child process's space via the exec system call, replacing bash
	- The command completes executing, and the child process dies via the exit system call
	- The parent shell is re-awakened by the death of the child process and proceeds to issue a new shell prompt
	- The parent shell then waits for the next command request from the user, at which time the cycle will be repeated.
- If a command is issued for **background processing** (`&`), it will execute the background process and issue a new shell prompt in parallel.
## Terminate a process
- Use command `kill -SIGKILL pid` or `kill -9 pid`.
- You can only kill your own processes.
- List all the kill signals: `kill -l`
- Command variants:
	- `kill -{number}`
	- `kill -{fullname}`
	- `kill -{shortname}`
- Terminate a process with name using `pkill`.
	- Ex: `pkill -KILL bash`
## User and Groups IDs
- Many users can access a system simultaneously, and each user can run multiple processes. The OS identifies the user who starts the process by the Real User ID (RUID) assigned to the user or Real Group ID (RGID).
## Priorities - Process niceness
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
- You can use **`CTRL-Z`** to suspend a foreground job and **`CTRL-C`** to terminate it.
- **`bg`** command is used to put process in the background and **`fg`** command to bring a background process into the foreground.
- `jobs` utility displays all jobs running in the background or the `jobs -l` for more detailed. The background jobs are connected to the terminal window, so if you log off, the `jobs` will not show the ones started from that window.
## Process status command
| Tool         | Purpose                                                                                                                                         |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **top**      | Process activity, dynamically updated                                                                                                           |
| **uptime**   | How long the system is running and the average load                                                                                             |
| **ps**       | Detailed information about processes                                                                                                            |
| **pstree**   | A tree of processes and their connections                                                                                                       |
| **mpstat**   | Multiple processor usage                                                                                                                        |
| **iostat**   | CPU utilization and I/O statistics                                                                                                              |
| **sar**      | Display and collect information about system activity                                                                                           |
| **numastat** | Information about NUMA (Non-Uniform Memory Architecture)                                                                                        |
| **strace**   | - Information about all system calls a process makes<br>- Log and display signals received by a process<br>- Diagnostic, learning and debugging |
| **lsof**     | List Open Files command display directory is in-use by process, user, ...                                                                       |
### `ps`
- System V Style:
	- `-u` displays information of processes for a specified username
	- `-ef` displays all the processes in the system in full detail
	- `-eLf` displays 1 line of information for every thread (a process can contain multiple threads)
- BSD style:
	- `aux` displays all processes of all users
	- `axo` specify which attributes you want to view
- `pmem`: Ratio of the process's resident set size to the physical memory on the machine, expressed as a percentage
- % CPU shows how much the CPU is used by the process, 100% means the process entire capacity 1 CPU core is used.
- Time shows how much time is used by the process. To get 1 second of CPU, the process needs to use 100% of CPU in 1 second. If it uses 50% of 1 CPU for 10 seconds then the time usage of the process is 5 seconds.
- The processes wrapped with square brackets are kernel processes run inside a privileged area in the kernel, which is managed kernel internally.
```shell
# u option to show user oriented format
ps u
# -U option to filter user
ps -U username
# l option to more detail, l is for long
ps lax
# f for forest display
ps fax
```
### `pstree`
- Displays the processes running on the system in the form of a tree diagram.
### `top`
- Helps user monitor the system performance live over time with constant real-time updates (2 seconds by default).
- The first line displays: uptime, number user logged on, load average.
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
### `pgrep`
- Process grep is used to filter process with name.
```shell
pgrep -a syslog # a option is to show all process with name syslog
```
### `nice`
- Launch a process with pre-set nice value: `nice - n [Nice Value] [Command]`.
- To assign lower value - higher priority, the root privilege is required.
### `renice`
- Assign new niceness value to a running process: `renice [New Nice Value] [PID]`.
### `ulimit`
- A built-in bash command that displays or resets process resource limits.
- Changes would affect the current shell. To make it affect all users, modify `/etc/security/limits.conf` and then reboot.
#### Types
- Hard limit `ulimit -H -n`.
- Soft limit:
	- User can modify but cannot exceed the hard limit
	- `ulimit -S -n`
### Start processes in the future
#### `at`
- Use `at` to execute any non-interactive command at a specified time **once**.
- To see what jobs are currently scheduled to run with `at`, use `atq`.
- To see detail of a job scheduled to run with `at`, use `atq -c {job_id}`.
- To remove a job scheduled to run with `at`, use `atrm {job_id}`.
#### `cron`
- `cron` is a time-based scheduling utility program well suited for repetitive jobs that execute once every few minutes, hours, day or specific time.
- Alternative way to setup `cron` is though special directory:
	- `/etc/cron.hourly`
	- `/etc/cron.daily`
	- `/etc/cron.weekly`
	- `/etc/cron.monthly`
- Step for setup script to special folder:
```shell
# 1. create script
touch script # don't use extension

# 2. cp script to special folder
sudo cp script /etc/cron.daily

# 3.set permission to ensure it is execuable
sudo chmod +rx /etc/cron.daily/script
```
- Can be found in the `/etc/crontab`.
	- `*`: match all possible values
	- `;`: match multiple value
	- `-`: range of value (ex: 2-4)
	- `/`: specifies step (ex: \*/4)
```shell
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed


# see crontab of the current user
crontab -l

# edit crontab of different user
sudo crontab -e -u other_user

# remove crontab
crontab -r
```
#### `anacorn`
- `anacorn` has replaced `cron` on modern system. If the machine was powered off, scheduled jobs would not run, `anacorn` will run the necessary jobs in a controlled and staggered manner when the system is up and running.
	- It can only run jobs every day(s), week, month, year not hour or minute.
	- If the machine was powered off, the `anacorn` will check for that job that has been run yet and run that job if it was not run.
- To schedule a job with `anacron`, edit the `/etc/anacrontab` file.
- To verify the job is correct, use `anacron -T`.
#### `sleep`
- `sleep` command is used to delay or suspend a job.
## Basic troubleshooting techniques
- Characterize the problem
- Reproduce the problem
- Always try the easy things first
- Eliminate possible causes one at a time
- Change only one thing at a time; if that does not fix the problem, change it back
- Check the system logs (**/var/log/messages**, **/var/log/secure**, etc.) for further information