- Key-value pairs attached to k8s objects (Pods, ReplicaSets, Nodes, Namespaces, ...).
- Used to organize and select a subset of object, based on the requirements in place.
- Controller use Labels to logically group together decoupled objets, rather than using object's names or IDs.
  ![[Pasted image 20240327135603.png]]
# Label Selectors
- Controller and Services use label selectors to select a subset of objects.
- Types
	- Equality-based selectors: using `=, ==, !=`.
	- Set-based selectors: using `in/notin.