## Messaging patterns
- Messaging services are reliable services often used for communication across heterogeneous systems that might be disconnected from the networking.
- Messaging protocols provide required communication between services through the messaging broker that guarantees the delivery and ordering of messages. The broker is responsible for persisting messages while the services is temporarily unavailable.
- Other patterns of leveraging messaging services are messaging orchestration, load balancing, and reactive programming.
- **Load balancing:** 
	- The number of messages depends on the publisher load. If the publisher implements a website that register user requests at the peak of the load, it will produce a large number of messages, the consumer may spend the entirety of peak and off-peak hours processing the message and this can delay processing.
	- To solve these issues, the number of consumers can be increased manually or dynamically based on the number of messages in the queue.
- **Competing consumers:**
	- The competing consumers pattern is a combination of the message broker pattern and load balancing pattern.
	- Designed to enable multiple producers and multiple consumers to coordinate with each other and increase the high availability and throughput of the whole solution.
	- Benefits:
		- Increases throughput with minimum effort that does not require code changes.
		- Improves the reliability of the solution because each of the services can be duplicated more than once.
		- Avoids complex configuration and message orchestration because the broker handles orchestration and supports out-of-the-box sequences.
		- Improves the scalability of the solution by increasing or decreasing the number of consumers and publishers depending on the load and queue length.
		- Saves const by enabling dynamic scaling.
		- Monitors and manages messages manually by leveraging queue explorers.
	- Considerations:
		- Messages should contain self-sufficient data to avoid affecting external changes in the parameters or the task after they have been submitted.
		- Message ordering is one of the functionalities provided out of the box, but you can change the order of processing by including the order ID in the task.
		- Any exceptions during task processing can be handled be leveraging a **"poison message"** strategy. For instance, for three unsuccessful attempts made to process messages by different consumers, it should mark the messages as poison messages and move those messages to another queue (a **dead-letter queue**) for further processing.
		- Retrying attempts should be used for pulling messages by consumers and pushing messages by publishers. The impact of potential connection loss can be eliminated by enabling retrying attempts.
## Azure Queue Storage
- The Azure Queue Storage provides storage with queue mechanics that supports a RESTfull interface.
- The queue persists messages and total capacity of these messages is close to petabytes. Meanwhile, the maximum size of the message is 64 KB.
- To provision a new queue, you first need to provision a new Azure Storage account and select the availability option, then create a new queue, and get a URL reference to use in REST request.
- Messaging from the code:
	- Receive and delete: this is the general approach to working with the queue. The receiver pulls the message of a batch of messages and starts to process the message. Then, when finished, it must explicitly delete the message. During the processing time, the message is locked or hidden and is not available to be received. 
	- Receive and update: sever can set a manual lock or hidden time per message to process messages that require extra time. The message must be explicitly deleted when the processing is finished successfully to avoid returning messages to the queue to be processed twice.
	- Peek: used for observing messages within the queue and usually receives a batch of messages to minimize overhead communication. You can peek at the messages to monitor the content of messages without generating a dequeuing message from the queue.
## Azure Service Bus
- The Azure Service Bus is a service that runs on Azure PaaS to meet enterprise messaging requirements.
- Scenarios the enterprise messaging service should be used for:
	- Message brokers
	- The transactional process: a transaction can include receiving and submitting multiple messages in multiple queues and keeping them hidden from other receivers until the transaction is committed.
	- Dynamic load balancing: the orchestration algorithm allows several consumers to receive messages from the queue and is dynamically scaled depending on the queue message length.
	- Message broadcast: the topics can support one or many relationships between publishers and consumers. Topics can be helpful when the messaging service required to engage in processing more than 1 service by processing a copy of the same message.
	- Relay communication: cloud services and hybrid applications can be safely connected without direct connection through the broker.
- Connectivity:
	- The publisher and subscriber should provide specific SAS keys (or policies) to be accepted by the server.
	- The policy allows Listen, Send and Manage activities accordingly and should be followed to the minimum privilege recommendations.
	- Azure Service Bus support RBAC.
- Advanced features:
	- Message sessions.
	- Message deferral.
	- Dead-letter queues (DLQs): support special treatment for undelivered and poisoned messages by storing them in a separate logical queue for further processing.
	- Deduplication: deletes duplicates of the message submitted by the publisher as a result of any connectivity issue. The duplicates are determined by their unique message IDs.
	- Transaction support: this allows the consumer to complete several operations within the scope of the transaction. Messages can be retrieved, processed and submitted in another queue or topic within the same transaction. If the transaction is canceled, all changes in the transaction scope will be reverted. If the transaction is completed, the changes will be visible to other customers.
	- Auto-forward: automatically forward the messages from the original queue to another queue or topic and removes the message from the originally received queue. The auto-forwarding can be configured only for the same namespace server.
	-  Idle auto-delete: automatically deletes the queue after a period of inactivity. The minimum interval can be set to 5 mins.
- Provision: create a namespace, then provide exact topic or queue.
## Relays
- A relay is an Azure PaaS resource that helps you to expose the services running in private networks to the service running in public clouds, or in another private network.
- The main advantage of Azure Relay is that it does not require any VON connections to be setup or any firewall to be configured. All traffic can be encrypted vis HTTPS or TCP sockets and transferred that way.
- Azure Relay supports 2 main technologies:
	- Hybrid Connection.
	- The WCF Relay service.
## Compare services
| Service | Main pattern | Pros | Cons |
| ------- | ------ | ------ | ------ |
| Event Hubs | Big data ingesting, streaming | High scale, event-persisting, capturing events, low-cost | An event can be received only once. Complex order support with multiple partitions. Server-managed cursor. |
| Event Grid | Pub/sub Reactive programming | Consumption-based price. Integration with other services. Topic support. | The events delivery sequence can be changed. |
| Queue Storage | Simple messaging | Unlimited queue size. Pay-as-you-go. Serverless architecture with geo-redundancy. | FIFO only. The small size of the message. No advanced messaging. The delivery sequence can be changed. |
| Service Bus | Enterprise messaging | Scalable service. Variety of advanced messaging services. Access management. | Cost 5 GB total message limit. |
| Azure Relay | Peer-to-peer communication | The communication is performed through a public network and does not require expensive VPN connectivity. | No message buffering. Both parties of communication must be online. |

