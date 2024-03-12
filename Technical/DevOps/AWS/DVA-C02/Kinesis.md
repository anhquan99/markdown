- Makes easy to collect, process, and analyze streaming data in real-time. Ingest real-time data.
# Streaming data
- Streaming data is data that is emitted at high volume in a continuous, incremental manner with the goal of low-latency processing.
# Kinesis service
- **Data streams:** capture, process, and store data streams.
- **Data Firehose:** load data stream into AWS data stores.
- **Data Analytic:** analyze data stream with SQL or Apache Flink.
- **Video Streams:** capture, process, and store video streams.
# Kinesis Data Streams
- Retention between 1 day to 1 year.
- Ability to reprocess (replay) data.
- Once data is inserted in Kinesis, it can not be deleted (immutability).
- Data that shares the same partition goes to the same shard (ordering).
- You can create data-processing applications, known as Kinesis Data Streams applications (consumer).
## High-level architecture
- The producers continually push data to Kinesis Data Stream, and the consumers process the data in real time.
  ![[Pasted image 20240305074334.png]]
## Terminology
- <mark style="background: #FFB86CA6;">Data record:</mark> the unit of data stored in a Kinesis data stream. Data records are composed of a sequence number, a partition key, and a data blob, which is an immutable sequence of bytes. Kinesis Data Streams does not inspect, interpret, or change the data in the blob in any way. A blob of data can be up to 1 MB.
- <mark style="background: #FFB86CA6;">Data stream application: </mark>
	- Is a consumer of a stream.
	- There are 2 types of consumers that you can develop: shared fan-out consumers and enhanced fan-out consumers.
- <mark style="background: #FFB86CA6;">Shard: </mark>
	- Is a uniquely identified sequence of data records in a stream.
	- A stream is composed of 1 or more shards, each of which provides a fixed unit of capacity.
	- Each shard can support up to 5 transactions per second for read, up to maximum total data read rate of 2 MB per second and up to 1000 record per second for writes, up to maximum total data write rate of 1 MB per second (including partition keys).
## Scenarios:
- Accelerated log and data feed intake and processing.
- Real-time metrics and reporting.
- Real-time data analytics.
- Complex stream processing.
## Resharding a Stream
- Resharding lets you adjust the number of shards in your stream to adapt to changes in the rate of data flow through the stream.
- Identify the hot or cold shards which is shards are receiving much more data, or much less data than expected by using metrics.
- Hot shards can be split to increase capacity, and cold shards can be merged to make better use of their unused capacity.
### Operations
- Splitting.
- Merging.
## Capacity modes
### Provisioned mode:
- You choose the number of shards provisioned, scale manually or using API.
- Each shard gets 1MB/s on (or 1000 records per second).
- Each shard get 2MB/s out (classic or enhanced fan-out consumer).
- You pay per shard provisioned per hour.
### On-demand mode:
- No need to provision or manage the capacity.
- Default capacity provisioned (4 MB/s in or 4000 records per second).
- Scales automatically based on observed throughput peak during the last 30 days.
- Pay per stream per hour and data in/out per GB.
## Security
- Control access / authorization using IAM policies.
- HTTPS, encryption at rest using KMS or client side encryption.
- VPC endpoints.
- Monitor API calls using CloudTrail.
## Kinesis producers
- Puts data records into data streams.
- Data record consists of:
	- Sequence number (unique per partition-key with shard).
	- Partition key (must specify while out records into stream).
	- Data blob (up to 1MB).
- Producers:
	- AWS SDK
	- Kinesis producer Library (KPL)
	- Kinesis Agent
- Write throughput: 1MB/sec or 1000 records/sec per shard.
- Use highly distributed partition key to avoid "hot partition".
- When **ProvisionedThroughputExceeded**:
	- Use highly distributed partition key.
	- Retries with exponential backoff.
	- Increase shards (scaling).
## Kinesis data streams consumers
- Get data records from data streams and process them.
### Consumers:
- <mark style="background: #BBFABBA6;">AWS Lambda</mark>
	- Supports classic and enhanced fan-out consumers.
	- Read records in batches.
	- Can config batch size and batch window.
	- If error occurs, Lambda retires until success or data expired.
	- Can process up to 10 batches per shard simultaneously.
- <mark style="background: #BBFABBA6;">Kinesis Data Analytics</mark>
- <mark style="background: #BBFABBA6;">Kinesis Data Firehose</mark>
- <mark style="background: #BBFABBA6;">Custom consumer (AWS SDK): </mark>
	- <mark style="background: #FFB8EBA6;">Shared (Classic) Fan-out consumer - pull:</mark>
		- Low number of consuming applications.
		- Read throughput: 2 MB/sec per shard across all consumers.
		- Max 5 `GetRecords` API calls/sec.
		- Latency ~ 200 ms.
		- Minimize cost.
		- Consumers poll data from Kinesis using `GetRecords` API call.
		- Returns up to 10 MB (then throttle for 5 seconds) or up to 10000 records.
	- <mark style="background: #FFB8EBA6;">Enhanced fan-out consumer - push:</mark>
		- Multiple consuming applications for the same stream.
		- 2 MB/sec per consumer per shard.
		- Latency ~ 70ms.
		- Kinesis pushes data to consumers over HTTP/2 (`SubscribeToShard` API)
		- Soft limit of 5 consumer applications (KCL) per data stream (default).
- <mark style="background: #BBFABBA6;">Kinesis Client Library (KCL)</mark> 
	- Java library that helps read record from a Kinesis Data Stream with distributed applications sharing the read workload.
	- Each shard is to be read by only 1 KCL instance.
	- Progress is checkpointed into DynamoDB (needs IAM access).
	- Track other workers and share the work amongst shards using DynamoDB.
	- KCL can run on EC2, Elastic Beanstalk, and on-premises.
	- Records are read in order at the shard level.
	- Versions:
		- KCL 1.x(supports shared consumer).
		- KCL 2.x (supports shared and enhanced fan-out consumer).
### Enhanced Fan-out
- Enables consumers to receive records from a stream with throughput up to 2 MB of data per second per shard.
- This throughput is dedicated, which means that consumers that use enhanced fan-out don't have to contend with other consumers that are receiving data from the stream.
- Kinesis Data Streams pushes data records from the stream to consumers that use enhanced fan-out, there for these consumers don't need to poll for data.
  ![[Pasted image 20240306063227.png]]
# Kinesis operations
- The same key will always go to the same shard.
## Shard splitting
- Used to increase the Stream capacity (1 MB/s data in per shard).
- Used to divide a hot shard.
- The old shard is close and will be deleted once the data is expired.
- No automatic scaling (manual increase/decrease capacity).
- Can't split into more than 2 shards in a single operation.
## Merging shards
- Decrease the Stream capacity and save costs.
- Can be used to group 2 shards with low traffic (cold shards).
- Old shards are closed and will be deleted once the data is expired.
- Can not merge more than 2 shards in a single operation.
# Kinesis Data Firehose
- Pay for data going through Firehose.
- Near real time
	- 60 secs latency minimum for non-full batches.
	- Or minimum 1 MB of data at a time.
- Supports many data formats, conventions, transformations, compression and custom data transformations using AWS Lambda.
- Can send failed or all data to a backup S3 bucket.

| Data Stream | Firehose |
| -- | -- | 
| Streaming service for ingest at scale | Load streaming data into other services |
| Write custom code (producer/consumer) | Fully managed | 
| Real-time (~200ms) | Near real-time (buffer time min 60 sec) |
| Manage scaling (shard splitting/merging) | Auto scaling |
| Supports replay capability | Does not support replay capability |
| Data storage 1 day to 1 year | |
# Kinesis data analytics
## SQL application
- Real-time analytics on Data Stream and Firehose using SQL.
- Add reference data from S3 to enrich streaming data.
- Fully managed, no servers to provision, autoscaling.
- Pay for actual consumption rate.
- Output:
	- Data Streams: create streams out of the real-time analytics queries.
	- Firehose: send analytics query results to destinations.
- Use cases:
	- Time-series analytics
	- Real-time dashboards
	- Real-time metrics
## Apache Flink
- Use Flink to process and analyze streaming data.
- Run any Apache Flink application on a managed cluster on AWS
	- Provisioning compute resources, parallel computation, automatic scaling.
	- Application backups (implemented as checkpoints and snapshots).
	- Use any Apache Flink programming features.
	- Flink does not read from Firehose (use SQL application instead).




