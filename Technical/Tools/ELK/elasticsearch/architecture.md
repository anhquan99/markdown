# Elasticsearch architecture
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