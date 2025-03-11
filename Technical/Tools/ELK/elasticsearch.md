# Elasticsearch
## Node
- Node is an instance of Elasticsearch that stores data. Each node will store a part of the data, which helps store data in multiple virtual or physical machines, exceeding the storage capacity of a single machine.
- A node refers to an instance of Elasticsearch and not a machine, so multiple nodes can be run on the same machine.
- Each node belong to a cluster - a collection of related nodes. Clusters are completely independent of each other by default. The separation of cluster is usually to separate things logically and configure differently.
- When a node start up, it will either join an existing cluster if configured to do, or create its own cluster consisting of just that node.
### Document
- Each unit of data that you store within a cluster is called a document - a JSON object.
- When you index a document, the original JSON object that you send to Elasticsearch is stored along with some metadata that Elasticsearch uses internally.
- The data you send to Elasticsearch is stored under field `_source`.
- Every document is stored within an index - a collection of documents that have similar characteristics and are logically related.
- Queries are run against indicies.