- Send 1 message to many receivers:
	- Direct integration
	- Pub/sub
- Up to 12500000 subscriptions per topic.
- 100000 topics limits.
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
# Message filtering
- JSON policy used to filter messages sent to SNS topic's subscriptions.
- If a subscription doesn't have a filter policy, it receives every message.