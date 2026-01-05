# Patterns

## Work/task queues

### Use cases
- Distribute time-consuming tasks among multiple clients (workers).
- Introduce async HTTP calls to RESTful services.
### Attribute
```
TODO mermaid
```
- **Round-robin dispatching**: send each message to the next consumer in a sequence.
- **Message acknowledgement**: as default, message delivered into consumer is marked for deletion.
- **No message timeouts**: RabbitMQ redeliver message when consumer dies 
- **Prefetch**: RabbitMQ dispatches message when it enters the queue, here we want consumer to handle one message at time.
### Setup
- Create an exchange with the type is `direct`.
- Create 2 queue with type is `classic`.
- Bind queues to the exchange.
## Publish/Subscribe (Fanout)
### Use cases
- Notifications
- Feeds
### Setup
- Create an exchange with the type is `fanout`.
- Create 2 queue with type is `classic`.
- Bind queues to the exchange.
## Publish/Subscribe based on Routing
### Use cases
- Notifications based on key
- Feeds based on key
### Attribute
- Direct exchange routes to specific queue
- Routing key is matched with binding key to route subset of messages to bound queues
- Many categories of message cause a lot of bindings - it complicates administration of RabbitMQ.
### Setup
- Create an exchange with the type is `direct`.
- Create 2 queues with type is `classic`. Bind queues with routing key.
- Create message with routing key.
## Publish/Subscribe based on Topics

### Use cases
- Notifications based on topic
- Feed based on topic
- Reduce bindings
### Attribute
- Topic is a kind of routing key defined as list of words, delimited by dots.
- Binding is a simple regular expression where * can substitute exactly one word # can substitute zero or more words.
- Topic limit is 255 character.
- Substitutions:
	- `*` exactly one word
	- `#` zero or more words
### Setup
- Create an exchange with the type is `direct`.
- Create 2 queues with type is `classic`. Bind queues with routing key.
- Create message with routing key.
## Publish/Subscribe based on Headers
### Use cases
- Notifications based on header.
- Feeds based on header.
### Attribute
- Uses headers from AMQP message structure
- No substitutions
- Ignores routing key
- More flexible than direct exchange
- Headers beginning with the string `x-` are not used to evaluate matches
	- `x-match: all`: must match all the headers to transfer to the queue
	- `x-match: any`: match any of the headers to transfer to the queue
### Setup
- Create an exchange with the type is `direct`.
- Create 2 queues with type is `classic`. Bind queues with headers - arguments.
- Create message with arguments.
## Remote Procedure Call (RPC)
### Use cases
- Out of the box RPC implementation
- Distribute tasks among multiple clients (workers) - and wait for result
### Attribute
- RabbitMQ can be used a nice RPC wrapper
- Single queue. Requests and responses goes through single queue both matched by correlationI id property.
- General RPC good habits:
	- Make a clear comment in the code that function call is local or remote
	- Keep up-to-date documentation about dependencies between components
	- Handle communication issues. Ex when RPC server is down, scale out RPC service by combining RPC pattern with Work Queues or more sophisticated and so on