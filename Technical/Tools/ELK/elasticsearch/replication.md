# Replication
## Definition
- Replication is configured at the index level.
- Replication works by creating copies of shards, referred to as replica shards.
- A shard that has been replicated, is called a **primary** shard.
- A primary shard and its replica shards are referred to as **a replication group**.
- Replica shards are a complete copy of a shard.
- A replica shard can serve search requests, exactly like its primary shard.
- The number of replicas can be configured at index creation.
- Replica shards are **never** stored on the same node as their primary shard.
![Replication](/Image/image-7.png)
## Choosing the number of replica shards
- How many replica shards are ideal, depends on the use case.
- Is the data stored elsewhere, such as in a RDBMS?
- Is it ok for data to be unavailable while you restore it?
- For mission-critical systems, downtime is not acceptable.
- Replicate shards once if data loss is not a disaster.
- For critical systems, data should be replicated at least twice.
## Increase query throughput with replication
- Replica shards of a replication group can serve different search requests simultaneously.
- ES knows how to route requests to the best shard
- CPU parallelization improves performance if multiple replica shards are stored on the same node.
```ad-example
With 2 nodes and 3 shards, a request can query an index in 3 shards at the same time. The node that have more than 1 shard will take advantages of CPU cores to query in parallel, this also means if the hardware has been fully ultilized it will not benefits the query.
```
- The health status of the cluster is affected from the allocation of the cluster.
```http
PUT /pages

GET /_cluster/health

GET /_cat/indices?v

GET /_cat/shards?v
```
- The Kibana indices are configured with a setting named `auto_expand_replicas`.
## Snapshots
- ES supports taking snapshots as backups.
- Snapshots can be used to restore to a given point in time.
- Snapshots can be taken at the index level, or for the entire cluster.
- Use snapshots for backups, and replication for high availability (and performance).