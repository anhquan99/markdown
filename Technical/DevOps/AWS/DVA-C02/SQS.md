- Fully managed service, used to decouple applications.
# Attributes
- Unlimited throughput, unlimited number of messages in queue.
- Default retention of message: 4 days, max 14 days.
- Limitation of 256KB per message sent.
- Can have duplicate message (at least once delivery, occasionally).
- Can have out of order message (the best effort ordering).
# Producing message
- SQS standard is unlimited throughput.
# Consuming message
- Poll SQS for message (receive up to 10 message at a time).
# Multiple consumers
- Consumers receive and process messages in parallel.
- Consumers can scale horizontally to improve throughput of processing with ASG.
# Security
## Encryption
- HTTPS
- At-rest encryption using KMS keys
- Client side encryption
## Access controls
- IAM policies to regulate access to the SQS API.
## SQS Access policies
- Similar to S3 bucket policies.
- Useful for cross-account access to SQS queues or allowing other services to write to an SQS queue.
# Message visibility timeout
- After a message is polled by a consumer, it becomes invisible to others consumers (default 30 seconds). Consumers can call the `ChangeMessageVisiility` API to get more time.
- If visibility timeout is high (hours), and consumer crashes, re-processing will take time. But if timeout is too low (seconds), the message could duplicate.
# FIFO
- Limited throughput 300 msg/s without batching 3000 msg/s.
- Exactly-once send capability (by removing duplicates).
- Messages are processed in order by the consumer.
## Deduplication
- Interval is 5 mins.
- Methods:
	- Content-based deduplication: hash message body with SHA-256.
	- Explicitly provide a Message Deduplication ID.
## Message grouping
- If you specify the same value of `MessageGroupID` in a SQS FIFO queue, you can only have 1 consumer, and all the messages are in order.
- To get ordering at the level of a subset of messages, specify different values for `MessageGroupID`
	- Messages that share a common Message Group ID will be in order within the group.
	- Each Group ID can have a different consumer (parallel processing).
	- Ordering across groups is not guaranteed.
# Dead letter queue (DLQ)
- If the message fails to process, it goes back to the queue for another try. After the `MaximumReceives` threshold is exceeded, the message goes into a DQL.
- Useful for debugging.
- DQL of a FIFO queue must also be a FIFO queue.
- DQL of a Standard queue must also be a Standard queue.
## Redrive to source
- Feature to help consume message in the DLQ to understand what is wrong with them
# Delay queue
- Delay message up to 15 mins.
- Default is 0 seconds.
- Can set a default at queue level.
- Can override the default on sending using the `DelaySeconds` parameter.
# Long polling
- When a consumer requests messages from the queue, it can optionally wait for messages to arrive if there are none in the queue.
- `LongPolling` decrease the number of API calls made to SQS while increasing the efficiency and decreasing the latency of your application.
- Long polling is preferable to short polling.
- Long polling can be enabled at the queue level or at the API level using `ReceiveMessageWaitTimeSeconds`.
# Extended client
- Due to the message size limit of the SQS message makes large message can not be sent. Extended client is used to send a large message to S3 and then send the small metadata of the message to the queue. The consumer will read the metadata of the message to retrieve the message from S3.
# SQS must know API
- `CreateQueue`, `DeleteQueue`
- `PurgeQueue` delete all the messages in queue
- `SendMessage(DelaySeconds), ReceiveMessage, DeleteMessage`
- `MaxNumberOfMessage`
- `ReceiveMessageWaitTimeSeconds` for long polling
- `ChangeMessageVisibility` change message timeout
- Batch APIs for `SendMessage, DeleteMessage, ChangeMessageVisibility` helps decrease your costs.