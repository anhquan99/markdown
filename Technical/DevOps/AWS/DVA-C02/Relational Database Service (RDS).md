- Managed DB service for DB use SQL as a query language:
	- Read replicas (up to 15 replicas) for improved read performance. AWS charge when data goes from one AZ to another, it does not apply within the same region.
	- Storage backed by EBS `gp2 or io1`
- Support:
	- Postgres
	- MySQL
	- MariaDB
	- Oracle
	- MS SQL Server
	- Aurora (AWS proprietary database)
# Instance class type
- General-purpose
- Memory-optimized
- Burstable-performance
- Optimized reads
# Instance storage
- General purpose SSD.
- Provisioned IOPS SSD: I/O intensive workloads.
- Magnetic
### Storage auto-scaling
- Increase storage on your RDS DB instance dynamically.
- You have to set maximum storage threshold (maximum limit for DB storage).
- Auto modify storage if:
	- Free storage is less than 10% of allocated storage.
	- Low-storage lasts least 5 minutes.
	- 6 hours have passed since last modification.
- Useful for unpredicted workloads.
## Amazone Aurora
- Postgres and MySQL are both supported as Aurora DB.
- AWS cloud optimized, claim to be 5x performance improvement than MySQL and 3x than Postgres.
- Can have up to 15 replicas and replication process is faster than MySQL (sub 10 ms replica lag).
- Failover is instantaneous.
- Cost more than RDS 20%.
- Backtrack: restore data at any point of time without using backups.
## Amazone RDS Proxy
- Managed service.
- Allows apps to pool and share DB connections established with the database.
- Improving database efficiency by reducing the stress on database resources and minimize open connections (and timeouts).
- RDS proxy is never publicly accessible (must be accessed from VPC),
# Amazon MemoryDB for Redis
## Usage
- MemoryDB for Redis is a durable, in-memory database for workloads that require an ultra-fast, primary database. You should consider using MemoryDB if your workload requires a durable database that provides ultra-fast performance (microsecond read and single-digit millisecond write latency). MemoryDB may also be a good fit for your use case if you want to build an application using Redis data structures and APIs with a primary, durable database. Finally, you should consider using MemoryDB to simplify your application architecture and lower costs by replacing usage of a database with a cache for durability and performance.
- ElastiCache for Redis is a service that is commonly used to cache data from other databases and data stores using Redis. You should consider ElastiCache for Redis for caching workloads where you want to accelerate data access with your existing primary database or data store (microsecond read and write performance). You should also consider ElastiCache for Redis for use cases where you want to use the Redis data structures and APIs to access data stored in a primary database or data store.
# Amazon ElasticCache
- Managed Redis or Memcached.
- Using ElasticCache involves heavy application code changes.
- Usage:
	- DB Cache
	- User session store
## Redis
### Components
#### Nodes:
- Node is the smallest building block of an ElasticCache deployment, fixed-size chuck of secure, network-attached RAM.
#### Shards:
- Grouping of 1 node to 6 related nodes.
#### Clusters:
- Logical grouping of 1 or more shards.
- Data is partitioned across the shards in a Redis cluster.
## Memcached
### Usage
- Simplest model possible.
- Run large nodes with multiple cores and threads.
- Scale out and in, adding and removing nodes as demand on system increases and decreases.
- Cache objects.

| Redis                                                    | Memcached                                      |     |
| -------------------------------------------------------- | ---------------------------------------------- | --- |
| Multi AZ with auto-failover                              | Multi-node for partitioning of data (sharding) |     |
| Read replicas to scale reads and have high availability  | No high availability (replication)             |     |
| Data durability using AOF (Append Only File) persistence | Non persistent                                 |     |
| Backup and restore features                              | No backup and restore                          |     |
| Supports set and sorted sets                             | Multi-threaded architecture                    |     |
- Consideration: ref https://aws.amazon.com/caching/best-practices/
### Caching pattern
- Lazy loading/ Cache-Aside/ Lazy population:
	- Cache data when cache is miss.
	- Pros:
		- Only requested data is cached.
		- Node failures are not fatal.
	- Cons:
		- Cache miss will result in 3 round trips.
		- Stale data.
- Write through:
	- Add or update cache when database is updated.
	- Pros:
		- Data in the cache is never stale, reads are quick.
	- Cons:
		- Missing data until it is added or updated. Should implement with Lazy loading.
		- Cache churn - data will never be read.
### Cache evictions
- Delete item explicitly.
- Memory full and LRU.
- Set TTL.