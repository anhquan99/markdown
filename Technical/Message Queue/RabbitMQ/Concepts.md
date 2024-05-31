- **Producing**: means sending, a program that sends messages is a **producer**.
- **Queue**: producers can send messages to 1 queue, **consumers** can try to receive data from 1 queue.
- **Consuming** has a similar meaning to receiving.
- **Consumer** is a program that mostly waits to receive messages.
- Ack: acknowledgement.
# Task Queues
- Avoid doing a resource-intensive task immediately and having to wait for it to complete, instead it will be scheduled to be done later.
# Messaging model
- The core idea in the messaging model in RabbitMQ is that the producer never sends any messages directly to a queue. Instead, the producer can only send messages to an exchange.
## Exchange
- Receives messages from producers.
- Pushes messages to queue.
- Must know exactly what to do with a message it receives, which is defined by the exchange type.
- The exchange is default when the user doesn't define the exchange.
- The relationship between an exchange and a queue is called binding, where the exchange sends messages to the queue.
### Types:
- Direct: deliver message by a routing key.
- Topic:
	- Message sent to a topic exchange can't have an arbitrary `routing_key` - it must be a list of words, delimited by dots.
	- Key bindings:
		- `*` can substitute for exactly one word. 
		- `#` can substitute for zero or more words. 
		- When a queue is bound with "`#`" binding key - it will receive all the messages, regardless of the routing key - like in `fanout` exchange.
		- When special characters "`*`" (star) and "`#`" (hash) aren't used in bindings, the topic exchange will behave just like a `direct` one.
- Headers
- Fanout: broadcasts all the messages to all the queue of the exchange.
## Temporary queues