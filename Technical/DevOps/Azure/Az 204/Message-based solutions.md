# Service Bus
- Protocols:
	- Advanced Message Queuing Protocol (AMQP) is the most efficient, because it maintains the connection to Service Bus.
	- Service Bus Messaging Protocol (SBMP) only available for .NET framework.
	- HTTP
## Queues
- Service Bus queues support a brokered messaging communication model.
-  Load-leveling enables producers and consumers to send and receive messages at different rates. The benefit for application or service is it can handle messages at average load instead of peak load.
- When using queues, components of distributed applications do not communicate directly with each other, instead they exchange messages via a queue, which acts as an intermediary (broker).
- A message producer (sender) hands off a message to the queue, after that a message consumer (receiver) pulls the message from the queue and processing it. The producer does not have to wait for a reply from the consumer.
- Consideration when queue:
	- Receive messages without having to poll the queue, Service Bus support long-polling receive operation using the TCP-based protocols
	- Required FIFO
	- Automatic duplicate detection
	- Your application process messages as parallel long-running streams (messages are associated with a stream using the session ID property on the message). In this model each node in the consuming node, the node can examine the state of the application stream state using transaction.
	- Required transactional behavior and atomicity when send or receiving multiple messages from a queue.
	- Handle message > 64 KB and < 256 KB.
- Receive modes:
	- Receive and delete: marks the message is consumed as the consumer request and return it to the consumer. This mode is not for failure tolerance.
	- Peek lock: if the message is unable to process, the Service Bus service abandon the message. Service Bus unlocks the message and make it available to be received again. There is a timeout associated with the lock.
		1. Find the next message to be consumed, lock is it to prevent other consumers from receiving it, then return to the application or service.
		2. After the application or service consumes the message, it will tell the message queue the message has been completed, then the message queue mark the message is consumed.
  ![[Pasted image 20231124200555.png]]
## Topics and subscriptions
- Service Bus topics and subscriptions support a publish/subscribe messaging communication model.
- When using topics and subscriptions, components of a distributed application do not communicate directly, instead they exchange messages via a topic, which acts as an intermediary.
### Advanced features:
- Message sessions
	- The message broker send message to receiver by session.![[Pasted image 20231124211040.png]]
	- Use patterns:
		- FIFO.
		- Request-response: enables the sender application to send a request and provides a way for the receiver to correctly send a response back to the sender application. This pattern typically needs a short-lived queue or topic for the application to send responses to. In this scenario, session provide a simple alternative solution with comparable semantics.
- Autoforwarding:
	- Autoforwarding feature enables you to chain a queue or subscription to another queue or topic that is part of the same namespace.
	- When autoforwarding is enabled, Service Bus automatically removes message that are placed in the first queue or subscription (source) and puts them in the second queue or topic (destination). It's still possible to send a message to the destination entity directly
- Dead-letter queue
- Scheduled delivery
- Message deferral
- Transactions
- Auto delete on idle
- Duplicate detection
- Support ordering
- Geo-disaster recovery
- Security
  ![[Pasted image 20231124200816.png]]
  ## Queue storage
  - Consideration when using storage queues
	- Store more than 80 GBs of message.
	- Required keeping track of the progress message