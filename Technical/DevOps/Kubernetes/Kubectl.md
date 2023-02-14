## Behind the scenes
1. Master node (control planen), the scheduler analyzes currently running Pods and finds the best Node for the new Pods(s).
2.  The newly created worker node will be moved to 1 current worker node.
3. The kubelet manages the Pods and containers (starting, checking health, ...).
## CLI
- `kubectl create deployment --image` create a deployment from image, this image must be in docker hub or in some repository.