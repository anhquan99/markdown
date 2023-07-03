# Azure support 2 types of queue mechanism
- Service Bus queues: is a fully managed enterprise integration message broker, which decouple applications and services.Data is transferred between different applications and services using messages.
	- Consideration when using:
		- Receive messages without having to poll the queue, Service Bus support long-polling receive operation using the TCP-based protocols
		- Required FIFO
		- Automatic duplicate detection
		- Your application process messages as parallel long-running streams (messages are associated with a stream using the session ID property on the message). In this model each node in the consuming node, the node can examine the state of the application stream state using transaction.
		- Required transactional behavior and atomicity when send or receiving multiple messages from a queue.
		- Handle message > 64 KB and < 256 KB.
	- Queues
		- Load-leveling enables producers and consumers to send and receive messages at different rates. The benefit for application or service is it can handle messages at average load instead of peak load.
	- Receive modes:
		- Receive and delete: marks the message is consumed as the consumer request and return it to the consumer. This mode is not for failure tolerance.
		- Peek lock: if the message is unable to process, the Service Bus service abandon the message. Service Bus unlocks the message and make it available to be received again. There is a timeout associated with the lock.
			1. Find the next message to be consumed, lock is it to prevent other consumers from receiving it, then return to the application or service.
			2. After the application or service consumes the message, it will tell the message queue the message has been completed, then the message queue mark the message is consumed.
		- Topics and subscriptions: provide on-to-many form of communication in a publish and subscribe pattern.
		- Rules and actions
- Storage queues
	- Consideration when using:
		- Store more than 80 GBs of message.
		- Required keeping track of the progress message