# Engine
## Routing
- When search or create new document, ES find the appropriate shard for the document or index the document to a shard, which is called routing.
- By default, `_routing` equals the ID of the document.
- The default routing strategy ensures that documents are distributed evenly.
- When changing number of shards, the routing formula return a different result with the old document leading the old document can not be found. Adding new shards requires creating a new index and reindex document into it. The **Split API** helps add shards with larger number of shard or **Shrink API** helps remove shards. 
 ![[image-9.png|422x418]]
##  Read and write data
## Read
- When a read request is received by a node, that node is responsible for forwarding it to the nodes that hold the relevant shards, collating the responses, and responding to the client. We call that node the _coordinating node_ for that request.\:
	1. Resolve the read requests to the relevant shards. Note that since most searches will be sent to one or more indices, they typically need to read from multiple shards, each representing a different subset of the data.
	2. Select an active copy of each relevant shard, from the shard replication group. This can be either the primary or a replica. By default, ES uses [[engine#Adaptive replica selection|adaptive replica selection]] to select the shard copies.
	3. Send shard level read requests to the selected copies.
	4. Combine the results and respond. Note that in the case of get by ID look up, only one shard is relevant, and this step can be skipped.
### Adaptive replica selection
- Response time of prior requests between the coordinating node and the eligible node.
- How long the eligible node took to run previous searches.
- Queue size of the eligible node's `search` threadpool.