- If multiple users and teams use the same k8s cluster, we can partition the cluster into virtual sub-cluster using Namespaces. The names of the resources/objects created inside a Namespace are unique, but not across Namespaces in the cluster.
- It provides a solution to the multi-tenancy requirement of today's enterprise development teams.
# Default namespaces
- `kube-system`: contains the objects created by the k8s system, mostly the control plane agents.
- `default`: contains the objects and resources created by admins and devs, and objects are assigned to it by default unless another Namespace name is provided by the user.
- `kube-public`: unsecured and readable by anyone, used for special purposes, such as exposing public (non-sensitive) information about the cluster.
- `kube-node-lease`: holds node lease objects used for node heartbeat data.