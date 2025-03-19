# Sharding
## Definition
- Sharding is a way to divide indices into smaller pieces, where each piece is referred to as a shard, and it is done at the index level.
- Used to horizontally scale the data volume.
- A shard has no predefined size, but it can only store up to about 2 billion documents.
```ad-note
A shard can be placed on any node, even mutiple shards in a single node.
```
![Sharding](/Image/image-6.png)
```ad-example
An index has 600GB of data and 2 nodes, each node has capacity 500GB. A single node can't handle amount data of the index. Sharding come to help where index is divied into 2 shards, each node can store a shard that requires 300GB of data, distributed data evenly to each node. 
```
## Usage
- Able to store more documents.
- Easier fit large indices onto nodes.
- Improve performance with parallelization of queries increases throughput of an index.
## Configure number of shards
- An index contains a single shard by default.
- Before ES 7.0, an index is created with 5 shards. However, having small indices is very unnecessary - over-sharding, and it was not possible to change the number of shards once an index has been created. When an index run out of storage, the solution is to create a new index with more shard, and move over the documents.
- Increase the number of shards with the Split API and reduce the number of shards with the Shrink API. It's still using the same solution, but ES handle the heavy lift.
- An index has millions of documents should consider add a couple of shards.