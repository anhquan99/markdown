# Database
- Non-relational databases might be right choice if:
	- Your application requires super-low latency.
	- Your data are unstructured, or you do not have any relational data.
	- You only need to serialize and deserialize data (JSON, XML, YAML, etc.).
	- You need to store a massive amount of data.
# Vertical scaling vs horizontal scaling
- Vertical scaling:
	- Hard limit. It is impossible to add unlimited CPU and memory to a single server.
	- Vertical scaling does not have failover and redundancy. If one server goes down, the website/app goes down with it completely.
# Database replication
- *“Database replication can be used in many database management systems, usually with a master/slave relationship between the original (master) and the copies (slaves)”*
- A master database should only support write operations, whereas a slave database gets copies of the data from the master database and only supports read operations.
- Applications require reads than writes, the number of slave databases in a system is usually larger than the number of master databases.
## Advantages
- Better performance
- Reliability
- High availability
# Cache
- Considerations:
	- When to use cache: when data is read frequently but modified infrequently. Cached data is stored in volatile memory, a cache server is not ideal for persisting data.
	- Expiration policy: it is advisable not to make the expiration date too short as this will cause the system to reload data from the database too frequently, and not too long as the data can become stale.
	- Consistency: keep the data store and the cache in sync.
	- Mitigating failures: a single cache server represent a potential single point of failure. Recommended approaches:
		- Use multiple cache server.
		- Overprovision the required memory by certain percentages.
	- Eviction policy: use eviction policy when the cache is full to remove existing items.
- 