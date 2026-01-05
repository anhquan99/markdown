# Data Safety 
Acknowledgements on both consumer and publisher side are important for data safety.
## Consumer acknowledgement
- ACK: positive acknowledgement
- nACK: extension for Reject multiple messages at once
- Reject: defined in AMQP protocol
- This feature is enabled by default
## Transaction (tx)
- Safe batching feature
- Note: decrease performance
## Publisher confirms
- Broker to send confirmation once message is safety stored