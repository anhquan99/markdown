# NoSQL databases
- NoSQL is used to store unstructured or semi-structured data as key-value pairs, broad columns, graphs or documents without relations, named non-relational database.
- Azure NoSQL platform supports: Gremlin, Cassadra and MongoDB API.
# Azure table storage
- Maximum size of a single entity is 1 MB and the maximum number of properties of an entity is 255.
- Azure table storage consists of the following components:
	- Account: provides an HTTPS-enabled endpoint of connection for RESTFul request.
	- Table: a collection of entities logically grouped.
	- Entity: collection of properties close to a data row but with a free schema of types and names for properties.
	- Property: combination of key-value pairs for storing NoSQL data.
# Azure Cosmos DB
- Advantages:
	- Highest SLA availabilities for dbs hosted in Azure.
	- Strong PaaS managed by Microsoft.
	- Serverless and provisioned throughput with the support of autoscaling.
	- It supports multiple consistency levels.
	- It has a flexible pricing model, including a free tier.
	- It supports well-know APIs.
- Provision: once you have finished provisioning, you can not change the API for your account.
	- Provisioned throughput: you will be charge a fixed amount per month.
	- Serverless platform: charge based on Request Units (RUs).
- Service characteristic:
	- High availability:
		- Allow writes across different regions, beneficial for reducing write latency in geographically distributed applications but it does not ensure write availability during a region outage.
		- A single write region with a service-managed failover is the ideal option for achieving high availability.
		- SLA: 99,999%.
	- Indexing:
		- The node or property in the documents is referenced by a path. Example: "/Customer/Name/?", "/Customer/*" .
			- The `*` includes all nested node.
			- The `?` includes only the exact value of the current node.
		- The default indexing policy indexes all properties.
		- Index policy:
			- A range index: is the default indexing policy. It is already optimized for best performance, and it suits a single field containing a list of string or number values.
			- A composite index: efficiency when filtering or ordering operations are performed on multiple fields.
			- A spatial index: use for geospatial objects, improve query performance and reduce RUs.
	- Time to live: remove documents from a container automatically after a specified time. 
- User-defined functions (UDFs):
	- Use for SQL API.
	- Created in JS.
	- Avoid when:
		- Cosmos DB already has functionality, it will always better than UDFs.
		- If query only has UDF in the `WHERE` filter. UDFs do not utilize the index and require loading documents. To improve and avoid loading document, combine a UDF and the additional filter option in the `WHERE` clause.
- Stored procedures.
- Triggers:
	- Pre-trigger: tigger before inserting or modifying an item.
	- Post-trigger: tigger after inserting or modifying an item.
- Optimistic concurrency:
	- SQL API supports Optimistic Concurrency Control (OCC). This control prevents clients from overriding the updates of others and losing valuable information as a result.
	- It supports OCC through an additional value, an `_etag` system property. It is updated every time a document changes and clients need to verify the `_etag` value, compare it with the current `_etag` value from db.  If the `_etag` value is different, the operation can override the previous changes.
- Encryption settings:
	- Data is encrypted at rest by default.
- Backups and recovery:
	- Continuous backup mode from past 30 days.
	- Periodic backup mode (default) allows a backup with a specific policy. Limited by month, backup intervals at a minimum of 1 hour. And backup need to be done by sending a request to the support team.
- Partitioning:
	- Scaling a container involves dividing data and throughput across physical partitions to provide multi-request processing.
	- Containers often have many logical partitions and just one physical partition.
	- A logical partition is a collection of documents that all share the same partition key.
	- The simple idea of partitions is they provide parallel execution of requests on the individual partition. Selecting a partition key is a critical choice that will impact the performance of your application, usually the partition key is one of the common field values that exist in most of the documents.
	- Idealy, documents should be divided between partitions. It almost avoids hot partitions, where almost the documents are in the same partition.
	- Queries performance is better when reading from the same partition, but you should also avoid most of the queries hitting the same partition, it makes the partition throughput hot and prevent the partition from properly scaling.
- Change feed: is an ordered log of changes in the container's documents.
- Consistency levels: (SQL API have 5 but others have 3)
	- Strong: offers a linearizability, it guarantees the sequence of operations and a read operation will return the latest value. When a write action is performed on the primary db, it is duplicated to the replica instances, only when replica have committed and confirmed then it will perform on primary.
	- Bounded stateless: guarantees the sequence of operation, but replicate asynchronous with a stateless window. Only allow maximum lag in operations or time.
	- Session (default): ensures all operations inside a user session are monotonic and consistent across primary and replica.
	- Consistent prefix: ensure updates appear in replicas in the right order and without gap.
	- Eventual: primary db does not wait for replica but, it will eventually perform operations the same as primary.
## Notes
- You need to use the `ReadItemAsync` method to read an item from the Azure Cosmos service, 
	- You need to provide:
		- `partitionKey`
		- `itemId`
	- The `consistencyLevel`,`eTag`, `sessionKey` parameter is part of the option `requestOptions` parameter of the `ReadItemAsync`.
- You plan to implement a storage mechanism for managing state across multiple change feed consumers. You need to configure the change feed processor in the .NET SDK for Azure Cosmos DB for NoSQL API.
	- Use **lease container**.
	- The **lease container** component serves as a storage mechanism to manage state across multiple change feed consumers.
	- The **delegate component** is the code within the client application that implements business logic for each batch of changes.
	- The **host component** is a client application instance that listens for changes from the change feed.
	- The **monitored container** component is monitored for any insert or update operations. It does not serve as a storage mechanism to manage state across multiple change feed consumers.
- You need to transition blobs in the Hot access tier to an online tier if blobs have not been modified in over 90 days
  ```` json
{
	"rules": [
		{
		"name": "agingRule",
		"enabled": true,
		"type": "Lifecycle",
		"definition": {
			"filters": {
				"blobTypes": [ "blockBlob" ],
				"prefixMatch": [ "sample-container/blob1" ]
			},
			"actions": {
				"baseBlob": {
				// code here
				}
			}
		}
	]
}
````
	- Use `"tierToCool": { "daysAfterModificationGreaterThan": 90 }`
	- The `tierToArchive` move the blobs to the archive tier which is not an online access tier it is an offline tier.