# Debug thread
- Go to **Debug | Windows | Threads**.
- Columns information:
	- **Flag**: If we want to keep track of a particular thread, we can flag it. This can be done by clicking on the flag icon. 
	- **ID**: This shows the unique identification number allocated to each thread. 
	- **Managed ID**: This shows the managed identification number assigned to each thread. 
	- **Category**: Every thread is assigned a unique category that helps us to identify whether it is a GUI thread (main thread) or a worker thread.
	- **Name**: This shows a name for each thread, or is displayed as .
	- **Location**: This assists in identifying where the thread is executing. We can drill down to see the complete call stack.
# Parallel stack windows
- Go to **Debug | Windows | Parallel Stacks**.
- Views:
	- Threads: shows call stacks for all threads running.
	- Tasks: use the `Tasks` view if we are using the Task Parallel Library to create `System.Threading.Tasks.Task` objects in our code.
# Parallel Watch window
- Go to **Debug | Windows | Parallel Watch**.
# Concurrency Visualizer
- Download from marketplace.
## Views
- Utilization
- Threads
- Cores