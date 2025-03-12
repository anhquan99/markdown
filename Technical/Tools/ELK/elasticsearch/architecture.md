# Elasticsearch Architecture
## Node
- Node is an instance of Elasticsearch (ES) that stores data. Each node will store a part of the data, which helps store data in multiple virtual or physical machines, exceeding the storage capacity of a single machine.
- A node refers to an instance of ES and not a machine, so multiple nodes can be run on the same machine.
- Each node belong to a cluster - a collection of related nodes. Clusters are completely independent of each other by default. The separation of cluster is usually to separate things logically and configure differently.
- When a node start up, it will either join an existing cluster if configured to do, or create its own cluster consisting of just that node.
### Document
- Each unit of data that you store within a cluster is called a document - a JSON object.
- When you index a document, the original JSON object that you send to ES is stored along with some metadata that ES uses internally.
- The data you send to ES is stored under field `_source`.
- Every document is stored within an index - a collection of documents that have similar characteristics and are logically related.
- Queries are run against indices.
### Node roles
#### Master
- This role is eligible, whereas a node may be elected as the cluster's master node.
- A master node is responsible for creating and deleting indices, among others.
- A node with this role will not automatically become the master node (unless there are no other master-eligible nodes).
- Useful for large clusters, where it requires dedicated master nodes.
- **Configuration:** `node.master`.
#### Data
- Enables a node to store data.
- Storing data includes performing queries.
- For relatively small clusters, this role is almost always enabled.
- Useful for having dedicated master nodes.
- Used as part of configuring a dedicated master node.
- **Configuration:** `node.data`
#### Ingest
- Enables a node to run ingest pipelines.
- Ingest pipelines are a series of step (processors) that are performed when indexing documents.
- A simplified version of Logstash, directly with ES.
- **Configuration:** `node.ingest`
#### Machine learning
- `node.ml` identifies a node as a machine learning node.
- `xpack.ml.enabled` enables the machine learning API for the node.
- Useful for running ML jobs that don't affect other tasks.
- **Configuration:** `node.ml`, `xpack.ml.enabled`
#### Coordination
- Coordination refers to the distribution of queries and the aggregation of results.
- Useful for coordination-only nodes (for large clusters).
- Configured by disabling all other roles.
#### Voting-only
- Participate in the voting for a new master node.
- Only useful for large clusters.
- **Configuration:** `node.voting_only`
#### When to change node roles?
- Useful for large clusters.
- When optimizing the cluster to scale the number of requests.
- Better understand what hardware resources are used for.
### Query cluster information
- ES cluster expose a REST API.
- Get cluster health:
```http
GET /_cluster/health
``` 
- Get node information:
```http
GET /_cat/nodes?v
```
- Get all cluster indices:
```http
GET /_cat/indices?v&expand_wildcards=all
```
- Send request with cURL
```bash
curl --cacert ca.crt -X GET -u elastic -H "Content-Type:application/json" https://localhost:9200/_search -d '{ "query": { "match_all": {} }}'
```
## Index
- Index is a logical namespace that holds a collection of documents, where each document is a collection of fields - key-value pairs that contain data.
- ES cluster like a database that contain many indices you can consider as table, and within each index, you have many documents.
	- RDBMS → Databases → Tables →  Columns/Rows
	- ES → Clusters → Indices → Shards → Documents with key-value pairs
- ES is built on top of Apache Lucene, each index is a Lucene index
## Document
- Documents are immutable.
- When update the document, behind the scene ES replaces the document when using update API.
	- The current document is retrieved.
	- The field values are changed.
	- The existing document is replaced with the modified document.