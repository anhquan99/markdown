- [[Develop solution Azure Cosmos DB | Cosmos DB]] alike.
- Scale to message workloads, distributed database.
- Millions of requests per seconds, trillions of row, 100 of TB of storage.
- Fast and consistent in performance (low latency on retrieval).
- Standard and Infrequent Access (IA) table class.
- Max size of an item (row) is 400KB.
- Data types supported are:
	- Scalar Types - string, number, binary, boolean, null.
	- Document types - list, map.
	- Set types - string set, number set, binary set.
# Primary keys
- Partition key (hash):
	- Partition key must be unique for each item.
	- Partition key must be diverse so that the data is distributed.
- Partition key + sort key (hash + range):
	- The combination must be unique for each item.
	- Data is grouped by partition key.
# Read/write capacity modes
- You can switch between different modes once every 24 hours.
## <mark style="background: #BBFABBA6;">Provisioned mode (default)</mark>
- Specify number of reads/writes per second.
- Pay for provisioned read and write capacity units.
- Table must have provisioned read and write capacity units.
- Option to setup auto-scaling of throughput to meet demand.
- Throughput can be exceeded temporarily using **Burst Capacity**.
- If Burst Capacity has been consumed, you will get a `ProvisionedThroughputExceeddedException`, if then advised to do an exponential backoff retry.
- Read capacity units (RCU), write capacity units (WCU).
- <mark style="background: #ADCCFFA6;">1 WCU represents 1 write per second for an item up to 1 KB in size. Example:</mark>
	- Write 10 items per second with item size 2 KB = $$10 * (2 KB / 1 KB) = 20 WCUs$$
	- Write 6 items per second with item size 4.5 KB = $$6 * (5 KB \text{(because 4.5 KB gets round up)} / 1 KB) = 30 WCUs$$
	- Write 120 items per minute with item size 2 KB = $$(120 / 60) * (2 KB / 1 KB) = 4 WCUs$$
- <mark style="background: #ADCCFFA6;">1 RCU represents 1 Strongly Consistent Read per second, or 2 Eventually Consistent Reads per second, for an item up to 4 KB in size, Example:</mark>
	- 10 strongly consistent reads per second with item size 4 KB = $$10 * ( 4 KB / 4 KB) = 10 RCUs$$
	- 16 eventually consistent reads per second with item size 12 KB = $$(16 / 2) * (12 KB / 4 KB) = 24 RCUs$$
	- 10 strongly consistent reads per second, with item size 6 KB = $$10 * (8 KB/ 4 KB) = 20 RCUs (\text{6 KB must round up to 8 KB})$$
## <mark style="background: #BBFABBA6;">On-demand mode</mark>
- Read/writes automatically scale up/down with your workloads.
- No capacity planning needed.
- Pay for what you use, more expensive.
# Consistent read
## <mark style="background: #BBFABBA6;">Eventually (default)</mark>
- Get some stale data because of replication.
## <mark style="background: #BBFABBA6;">Strongly</mark>
- If we read just after a write, we will get the correct data.
- Consumes twice the RCU.
# Partitions internal
- Data is stored in partitions.
- To compute number of partitions
	- $$\text{Number of partitions by capacity} = (RCUsTotal / 3000) + (WCUsTotal / 1000))$$
	- $$\text{Number of partitions by size} = (TotalSize / 10 GB))$$
	- $$\text{Number of partitions} = ceil(max(\text{Partition by capacity}, \text{Partition by size})$$
- WCUs and RCUs are spread evenly across partitions.
# Throttling
- Reasons
	- Hot keys: one partition key is being read too many times
	- Hot partitions
	- Very large items: RCU and WCU depends on size of items
- Solutions
	- Exponential backoff
	- Distribute partition keys as much as possible
	- If RCU issue we can use DynamoDB Accelerator (DAX)
# Writing data
- `PutItem`: creates a new item or fully replace an old item - consume WCUs.
- `UpdateItem`: edits existing item's attribute or adds a new item if it doesn't exist. Can be used to implement Atomic Counter - numeric attribute that's unconditionally incremented.
- Conditional writes:
	- Accept a write/update/delete only if conditions are met, otherwise returns an error.
	- Helps with concurrent access to items.
	- No performance impact.
# Reading data
- `GetItem`
- Query
	- Return data based on:
		- `KeyConditionExpression`
			- Partition key value (must be = operator) - required.
			- Sort key value `(= , <, <=, >, >=, Between, Begins with`) - optional.
		- `FilterExpression`
			- Additional filtering after query operation (before data retuned to you).
			- Use only with non-key attributes (does not allow Hash or Range attribute)/
	- Returns:
		- The number of items specified in Limit
		- Or up to 1 MB of data
	- Ability to do pagination on the results.
	- Can query table, a Local Secondary Index, or a Global Secondary Index.
- Scan
	- Scan the entire table and then filter out data (inefficient).
	- Returns up to 1 MB of data - use pagination to keep on reading.
	- Consumes a lot of RCU.
	- Limit impact using `Limit` or reduce the size of the result and pause.
	- For faster performance, use **Parallel Scan**
		- Multiple workers scan multiple data segments at the same time
		- Increase the throughput and RCU consumed
		- Limit the impact of parallel scans just like you would for Scans
	- Can use `ProjectionExpression and FilterExpression` (no changes to RCU).
# Deleting data
- `DeleteItem`
- `DeleteTable`
# Batch operations
-  Help to reduce the number of API calls.
- Operations are done in parallel for better efficiency.
- Part of a batch can fail, in which case we need to try again for failed items.
- `BatchWriteItem`
	- Up to 25 `PuteItem` and/or `DeleteItem` in 1 call.
	- Up to 16 MB of data written, up to 400 KB of data per item.
	- Can't update items (use `UpdateItem`).
	- `UnprocessedItems` for failed write operations (exponential backoff or add WCU).
- `BatchGetItem`
	- Return items from 1 or more tables.
	- Up to 100 items, up to 16 MB of data.
	- Items are retrieved in parallel to minimize latency.
	- `UnprocessedItems` for failed write operations (exponential backoff or add WCU).
# PartiQL
- SQL alike.
- Run PartiQL queries from:
	- AWS Mangement Console
	- NoSQL WorkBench for DynamoDB
	- DynamoD APIs
	- AWS CLI
	- AWS SDK
# Conditional writes
- For `PutItem, UpdateItem, DeleteItem and BatchWriteItem`
- Optimistic locking
	- A strategy to ensure an item has not changed before you update/delete it.
	- Each item has an attribute that acts as a version number.
- Specify a condition expression:
	- `attribute_exists`
	- `attribute_not_exists`
	- `attribute_type`
	- `contains` (for string)
	- `begins_with` (for string)
	- `in, and between`
	- `size` (string length)
- Note: Filter expression filters the results of read queries, while condition expressions are for write operations.
# Local Secondary Index (LSI)
- Alternative sort key for table (same partition key as that of base table).
- The sort key consists of 1 scalar attribute (string, number, or binary).
- Up to 5 local secondary indexes per table.
- Must be defined at table creation time.
- Attribute projections can contain some or all the attributes of the base table (`KEYS_ONLY, INCLUDE, ALL`).
# Global Secondary Index(GSI)
- Alternative primary key (hash or hash + range) from the base table.
- Speed up queries on non-key attributes.
- The index key consists of scalar attributes (string, number, or binary).
- Attribute projections - some or all the attributes of the base table (`KEYS_ONLY, INCLUDE, ALL`).
- Must provision RCUs and WCUs for the index.
- Can be added/modified after table creation.
# Indexes and throttling
## <mark style="background: #FF5582A6;">GSI</mark>
- If the writes are throttled on the GSI, then the main table will be throttled, even if the WCU on the main tables are fine.
- Choose your GSI partition key and WCU capacity carefully!
## <mark style="background: #FF5582A6;">LSI</mark>
- Uses the WCUs and RCUs of the main table.
- No special throttling considerations.
# DAX
- Fully managed cache for DynamoDB.
- 5 mins TTL for cache (default).
- Up to 10 nodes in the cluster.
- For individual object cache, query and scan cache.
# Streams
- Ordered stream of item-level modification (create/update/delete) in a table.
- Data retention for up to 24 hr.
- Choose the information is going to be written to the stream:
	- `KEYS-ONLY`
	- `NEW_IMAGE` entire item as it appears after it was modified
	- `OLD_IMAGE` entire item as it appeared before it was modified
	- `NEW_AND_OLD_IMAGES` both the new and the old images of the item
- Stream are made of shards, just like Kinesis Data Streams.
- Automated provisioned by AWS.
# TTL
- Automatically delete items after an expiry timestamp.
- Does not consume any WCUs.
- TTL attribute must be a number data type with **Unix Epoch timestamp** value.
# Good to know
- `--projection-expression`: 1 or more attribute to retrieve.
- `--filter-expression`: filter items before retuned to you.
- Pagination
	- `--page-size`
	- `--max-items`
	- `--starting-token`
# Transactions
- Consume 2x WCUs and RCUs.
- Operations: `TransactGetItesm` and `TransactWriteItems`.
- Example:
	- 3 transactional writes per second with item size 5 KB = $$3 * ( 5 KB / 1 KB) * 2 (\text{transactional cost}) = 30 WCUs$$
	- 5 transactional reads per second with item size 5 KB = $$5 * ( 8 KB / 4 KB) * 2 (\text{transactional cost}) = 20 WCUs$$
# Write sharding
- A strategy that allows better distribution of items evenly across partitions by adding a suffix to partition key value.
- **Methods**
	- Sharding using random suffix
	- Sharding using calculated suffix
# Write types
- **Concurrent**: the second write overwrites the first write.
- **Atomic**: both writes succeed.
- **Conditional**: the 1st write is accepted, 2nd write fails.
- **Batch**: write/update many items at a time.
# Operations
## <mark style="background: #BBFABBA6;">Table cleanup</mark>
- Option 1: `Scan` + `DeleteItem` - very slow, consumes RCU and WCU, expensive.
- Option 2: Drop table + Recreate table - fast, efficient, cheap.
## <mark style="background: #BBFABBA6;">Copying table</mark>
- Option 1: using AWS Data Pipeline.
- Option 2: backup and restore into a new table - take some time.
- Option 3: `Scan` + `PutItem` or `BatchWriteIterm` - write your own code.