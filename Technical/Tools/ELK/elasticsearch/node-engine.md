# Engine
## Routing
- When search or create new document, ES find the appropriate shard for the document or index the document to a shard, which is called routing.
- By default, `_routing` equals the ID of the document.
- The default routing strategy ensures that documents are distributed evenly.
- When changing number of shards, the routing formula return a different result with the old document leading the old document can not be found. Adding new shards requires creating a new index and reindex document into it. The **Split API** helps add shards with larger number of shard or **Shrink API** helps remove shards. 

![Routing formular|422x418](/Image/image-8.png)
##  Read and write data
- ES data replication model is based on the **primary-backup model**.
- The primary shard serves as the main entry point for all indexing operations, which in charge of validating and making sure indexes are correct.
### Read
- When a read request is received by a node, that node is responsible for forwarding it to the nodes that hold the relevant shards, collating the responses, and responding to the client. We call that node the _coordinating node_ for that request.\:
	1. Resolve the read requests to the relevant shards. Note that since most searches will be sent to one or more indices, they typically need to read from multiple shards, each representing a different subset of the data.
	2. Select an active copy of each relevant shard, from the shard replication group. This can be either the primary or a replica. By default, ES uses [[engine#Adaptive replica selection|]] to select the shard copies.
	3. Send shard level read requests to the selected copies.
	4. Combine the results and respond. Note that in the case of get by ID look up, only one shard is relevant, and this step can be skipped.

![Elasticsearch read model|178x258](/Image/elk-read-model.excalidraw.png)
#### Adaptive replica selection
- Response time of prior requests between the coordinating node and the eligible node.
- How long the eligible node took to run previous searches.
- Queue size of the eligible node's `search` threadpool.
### Write
- Since the replicas can be offline, the primary shard is not required to replicate to all replicas. Instead, ES maintains a list of shard copies that should receive the operation which is set of **good** shards that are guaranteed to have processed all the operations that have been acknowledged to the user, called **in-sync copies** and is maintained by the master node
- The primary shard follows basic flow:
	1. Validate incoming operation and reject it if structurally invalid.
	2. Execute the operation locally (index, delete, ...), it also validates the content of the fields and reject if needed.
	3. Forward the operation to each replica in the current in-sync copies set in parallel.
	4. Once all in-sync replicas have successfully performed the operation and responded to the primary, the primary acknowledges the successful completion of the request to the client.
![](/Image/image-9.png)
### Shard failure recovery
#### Primary terms
- A way to distinguish between old and new primary shards.
- Essentially a counter for how many times the primary shard has changed.
- The primary term is appended to write operations.
#### Sequence numbers
- Appended to write operations together with the primary term
- Essentially a counter that is incremented for each write operation
- The primary shard increases the sequence number
- Enables ES to order write operations
#### Recovery
- Primary terms and sequence numbers are key when ES needs to recover from a primary shard failure
	- Enables ES to more efficiently figure out which write operations need to be applied
- For large indices, this process is really expensive, to speed things up, ES uses checkpoints.
#### Global and local checkpoints
- Each replication group has a global checkpoint.
- Each replica shard has a local checkpoint
- **Global checkpoints** are the sequence number that all active shards within a replication group have been aligned at **least up to**. If a primary shard fails and rejoins the cluster at a later point, ES only needs to compare the operations that are above the global checkpoint that it last knew about.
- **Local checkpoints** are the sequence number for the last write operation that was performed. If a replica shard fails, only operations that have a sequence number higher than its local checkpoint need to be applied when it come back. 
## Optimistic Concurrency Control
### Legacy
- Using `version` number to handle concurrency.
### Current version
- Using sequence number and primary term.