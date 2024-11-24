# Scale from 0 to millions of users
## Database
- Non-relational databases might be right choice if:
	- Your application requires super-low latency.
	- Your data are unstructured, or you do not have any relational data.
	- You only need to serialize and deserialize data (JSON, XML, YAML, etc.).
	- You need to store a massive amount of data.
## Vertical scaling vs horizontal scaling
- Vertical scaling:
	- Hard limit. It is impossible to add unlimited CPU and memory to a single server.
	- Vertical scaling does not have failover and redundancy. If one server goes down, the website/app goes down with it completely.
## Database replication
- *“Database replication can be used in many database management systems, usually with a master/slave relationship between the original (master) and the copies (slaves)”*
- A master database should only support write operations, whereas a slave database gets copies of the data from the master database and only supports read operations.
- Applications require reads than writes, the number of slave databases in a system is usually larger than the number of master databases.
### Advantages
- Better performance
- Reliability
- High availability
## Cache
- Considerations:
	- When to use cache: when data is read frequently but modified infrequently. Cached data is stored in volatile memory, a cache server is not ideal for persisting data.
	- Expiration policy: it is advisable not to make the expiration date too short as this will cause the system to reload data from the database too frequently, and not too long as the data can become stale.
	- Consistency: keep the data store and the cache in sync.
	- Mitigating failures: a single cache server represent a potential single point of failure. Recommended approaches:
		- Use multiple cache server.
		- Overprovision the required memory by certain percentages.
	- Eviction policy: use eviction policy when the cache is full to remove existing items.
## Database scaling
### Vertical
- Scaling by adding more power.
- Drawbacks:
	- You can't add more resource once you maxed out.
	- Greater risk of single point of failures.
	- High cost.
### Horizontal
- Sharding separates large databases into smaller, more easily managed parts called shards. Each shard shares the same schema, though the actual data on each shard is unique to the shard.
- When choosing a sharding key, one of the most important criteria is to choose a key that can evenly distributed data.
#### Challenges
- Resharding data is needed when:
	- A single shard could no longer hold more data due to rapid growth.
	- Certain shards might experience shard exhaustion faster than others due to uneven data distribution. When shard exhaustion happens, it requires updating the sharding function and moving data around.
- Celebrity problem: this is also called a hotspot problem. Excessive access to specific shard could cause server overload.
- Join and de-normalization: once a database has been sharded across multiple servers, it's hard perform join operations across database shards. A common workaround is to de-normalize the database so that queries can be performed in a single table.
## Millions of users and beyond
- Scaling a system is an iterative process.
- Summary:
	- Keep web tier stateless
	- Build redundancy at every tier
	- Cache data as much as you can
	- Support multiple data centers
	- Host static assets in CDN
	- Scale your data tier by sharding
	- Split tiers into individual services
	- Monitor your system and use automation tools