# Control Plane
- Provides a running environment for the control plane agents responsible for managing the state of a K8s cluster, and it is the brain behind all operations inside the cluster.
- The control plane components are agents with very distinct roles in the cluster's management.
# Fault tolerance
- It's important to keep the control plane running at all costs. Losing the control plane may introduce downtime, causing service disruption to clients, with possible loss of business.
- To ensure the control plane's fault tolerance, control plane node replicas can be added to the cluster, configured in High-Availability (HA) mode. While only one of the control plane nodes is dedicated to actively managing the cluster, the control plane components stay in sync acorss the control plane node replicas.
- To persist the K8s cluster's state, all cluster configuration data is saved to a distributed key-value store which only holds cluster state related data.
- The key-value store may be configured on the control plane node (stack topology), or on its dedicated host (external topology) to reduce the changes of data store loss by decoupling it from other control plane agents.
## Node Components
- Essential components:
	- [[control-plane#API Server|API Server]]
	- [[control-plane#Scheduler|Scheduler]]
	- [[control-plane#Controller Managers|Controller Manager]]
	- [[control-plane#Key-Value Data Store|Key-Value Data Store]]
- Additional components:
	- Container runtime
	- Node agent
	- Proxy
	- Optional add-ons for cluster-level monitoring and logging
### API Server
- All the administrative tasks are coordinated by the `kube-apiserver` - a central control plane component running on the control plane node.
- The API Server intercepts **RESTfull** calls from users, then validates and processes them.
- Durning processing the API Server reads the K8s cluster's current state from the key-value store, and after a call's execution, the resulting state of the K8s is saved in the key-value store for persistence.
- **It is the only component can communicate (both read and save) to the key-value store.**
#### API directory
- Directory tree
  ![[Pasted image 20240324184154.png]]
- Group types:
	- **Core group `(/api/v1)`**: includes objects such as Pods, Services, Nodes, Namespaces, ConfigMaps, Secrets, ...
	- **Name group**: Includes objects in `/apis/$NAME/$VERSION` format. These different API versions imply different levels of stability and support:
		- Alpha-level: may be dropped at any point in time, without notice. Ex: `/apis/batch/v2alpha1`
		- Beta-level: well-tested, but semantics of objects may change in incompatible ways in a subsequent beta or stable release. Ex: `/apis/certificates.k8s.io/v1beta1`.
	- **System-wide**: consists of system-wide API endpoints, like `/healthz`, `/logs`, `/metrics`, `/ui`, ...
### Scheduler
- The role of the `kube-scheduler` is to assign new workload objects such as pods encapsulating container, to nodes - typically worker nodes.
- During the scheduling process, decisions are made based on current K8s current cluster state (via [[control-plane#API Server|API Server]]) and new workload [[object|object's]] requirements (which are part of its configuration data). Requirements may include constraints that users and operator set, such as scheduling work on a node labeled with `disk==ssd` key-value.
- The scheduler also takes into account Quality of Service (QoS) requirements, data locality, affinity, anti-affinity, taints, toleration, cluster topology, ...
- Once all the cluster data is available, the scheduling algorithm filters the nodes with predicates to isolate the possible node candidates, which then are scored with priorities in order to select the one node that satisfies all the requirements for hosting the new workload. The outcome of the decision process is communicated back to the API Server, which then delegates the workload deployment with other control plane agents.
### Controller Managers
- Used to regulate the state of the K8s cluster.
- Controllers are watch-loop processes continuously running and comparing the cluster's desired state (object's config data) with its current state (key-value store from [[control-plane#API Server|API Server]]).
#### Responsible:
- To act when nodes become unavailable, to ensure container pod counts are as expected, to create endpoints, service accounts, and API access tokens.
- To interact with the underlying infrastructure of a cloud provider when nodes become unavailable, to manage storage volumes when provided by a cloud service, and to manage load balancing and routing.
### Key-Value Data Store
- Use `etcd`, which is a strongly consistent, distributed key-value data store used to persist a K8s cluster's state.
- New data is written to the data store only by appending to it, data is never replaced in the data store, obsolete data is compacted (or shredded) periodically to minimize the size of the data store.
- `etcd` CLI management tool - `etcdctl`, provides snapshot save and restore capabilities.
- Stacked `etcd` topology
  ![[Pasted image 20240324122949.png]]
- External `etcd` topology
  ![[Pasted image 20240324123013.png]]
- Both stacked and external topologies support HA configs. `etcd` is based on the [Raft Consensus Algorithm](https://web.stanford.edu/~ouster/cgi-bin/papers/raft-atc14), which allows a collection of machines to work as a coherent group that can survive the failures of some of its members. At any given time, 1 of the nodes the group will be the leader. `etcd` gracefully handles leader elections and can tolerate node failure, including leader node failures. Any node can be treated as a leader.
  ![[Pasted image 20240324123519.png]]