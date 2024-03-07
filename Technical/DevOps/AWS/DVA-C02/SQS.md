- Fully managed service, used to decouple applications.
# Architecture
- There are 3 main parts in a distributed messaging system:
	- Components of distributed system.
	- Queue.
	- Message in the queue.
# Types
## Standard queue 
### Attributes:
- Unlimited throughput: standard queues.
- At-least-once delivery: a message is delivered at least once, but occasionally more than one copy of a message is delivered.
- Best-effort-ordering: occasionally, messages are delivered in an order different from which they were sent.
### Best practices
- Working with messages:
	- Processing messages in a timely manner.
	- Handling request errors: retry and backoff.
	- Setting up long polling.
	- Capturing problematic messages: configure dead-letter queue for messages that can't be processed.
	- Setting up dead-letter queue retention.
	- Avoiding inconsistent message processing.
	- Implementing request-response system:
		- Don't create reply queues per message. Instead, create reply queues on startup, per producer, and use a correlation ID message attribute to map replies to requests.
		- Don't let your producers share reply queues. This can cause a producer to receive response messages intended for another producer.
## FIFO queue:
### Attributes:
- High throughput: support up to 3000 messages per second.
- Exactly-once processing: a message is delivered once and remains available until a consumer processes and deletes it. Duplicates aren't introduced into the queue.
- FIFO delivery.
### Best practices:
- Using the message deduplication ID:
	- Providing the message deduplication ID
	- Enabling deduplication for a single-producer/consumer system
	- Designing for outage recovery scenarios.
	- Working with visibility timeouts.
- Using the message group ID:
	- Interleaving multiple ordered message groups.
	- Avoid processing duplicates in a multiple producer/consumer system.
	- Avoid having a large backlog of messages with the same message group ID.
	- Avoid reusing the same message group ID with virtual queues.
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