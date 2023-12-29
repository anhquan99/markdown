- Managed DB service for DB use SQL as a query language:
	- Read replicas (up to 15 replicas) for improved read performance. AWS charge when data goes from one AZ to another, it does not apply within the same region.
	- Storage backed by EBS `gp2 or io1`
- Support:
	- Postgres
	- MySQL
	- MariaDB
	- Oracle
	- Ms SQL Server
	- Aurora (AWS proprietary database)
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
- Cost more thang RDS 20%.
- Backtrack: restore data at any point of time without using backups.
## Amazone RDS Proxy
- Managed service.
- Allows apps to pool and share DB connections established with the database.
- Improving database efficiency by reducing the stress on database resources and minimize open connections (and timeouts).
- RDS proxy is never publicly accessible (must be accessed from VPC),
# Amazone ElastiCache
- Managed Redis or Memcached.
- Using ElastiCache involves heavy application code changes.
- Usage:
	- DB Cache
	- User session store

| Redis | Memcached |
| --- | --- |
| Multi AZ with auto-failover | Multi-node for partitioning of data (sharding) |
| Read replicas to scale reads and have high availability | No high availability (replication)| 
| Data durability using AOF (Append Only File) persistence | Non persistent |
| Backup and restore features | No backup and restore |
| Supports set and sorted sets | Multi-threaded architecture |
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
- 