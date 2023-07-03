- It's a fully managed NoSQL database designed to provide low latency, elastic scalability of throughput, well-defined semantics for data consistency, and high availability.
- Consistency levels
	 ![[Pasted image 20230610211654.png]]
	 - Strong consistency: Users are always guaranteed to read the latest committed write.
	 - Bounded staleness consistency: the reads might lag behind writes by at most "K" versions of an item or by "T" time interval, whichever is reached first.
	 - Session consistency: monotonic reads and writes.
	 - Consistent prefix consistency: if the operation performs on doc_1 and doc_2 you will get doc_1 ver 1 and doc_2 ver 1 or doc_1 ver 2 and doc_2 ver 2 but never doc_1 ver 1 and doc_2 ver 2.
	 - Eventual consistency: there's no guarantee for reads. In the absence of any further writes, the replicas eventually converge. It's the weakest form of consistency.
- Request units (RUs):
	  ![[Pasted image 20230610214109.png]]
	- Represents the system resources such as CPU, IOPS, and memory that are required to perform the database operations supported by Azure Cosmos DB.
	- Azure Cosmos DB account modes:
		- Provisioned throughput mode: you provision the number of RUs for your application.
		- Serverless mode: you get billed for the number of RUs consumed.
		- Autoscale mode