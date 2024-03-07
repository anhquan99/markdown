- Send 1 message to many receivers:
	- Direct integration
	- Pub/sub
- Up to 12500000 subscriptions per topic.
- 100000 topics limits.
# Model
- Application-to-application (A2A) messaging.
- Application-to-person (A2P) messaging.
# How to publish
- Topic publish (using the SDK)
	- Create a topic
	- Create a subscription
	- Publish to the topic
- Direct publish (for mobile apps SDK)
	- Create a platform application
	- Create a platform endpoint
	- Publish to the platform endpoint
	- Works with Google GCM, Apple APNS, Amazon ADM, ...
# Security
- Similarly to SQS
## Best practices
- Ensure topics aren't publicly accessible.
- Implement least-privilege access.
- Use IAM roles for applications and AWS services which require Amazon SNS access.
- Implement service-side encryption.
- Enforce encryption of data in transit.
- Consider using VPC endpoints to access Amazon SNS.
- Ensure subscriptions are not configured to deliver to raw http endpoints.
# Pattern
## SNS + SQS = Fan out
- Fully decoupled, no data loss.
- SQS allows for data persistence, delayed processing and retries of work.
- Add more SQS subscribers overtime.
- Cross-Region Delivery: works with SQS queues in other regions.
## S3 Event to multiple queues
- The same as fan out but send S3 event.
## SNS to S3 through Kinesis Data Firehose
- SNS can send to Kinesis.
# FIFO topic
- Similar to SQS FOFO
- SNS FIFO + SQS FIFO = Fan out (ordering + deduplication).
- Message can be archived up to 365 days.
# Message filtering
- JSON policy used to filter messages sent to SNS topic's subscriptions.
- If a subscription doesn't have a filter policy, it receives every message.
# Message batching
- Using the SNSS `PublishBatch` API to publish up to 10 messages in a single API request.
- The total aggregate size of all messages that you send in a single `PublishBatch` API request can't exceed 256 KB.