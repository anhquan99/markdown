# Role of event-driven solutions
- **Event:** a small piece of valuable data that contains information about what is happening. Usually, the event body is represented in JSON format.
- **Event source or publisher:** the services where the event takes place.
- **Event consumer, subscriber, or handler:** the services that receive the event for processing or react to the event.
- **Event schema:** the key-value pairs that must be provided in the event body by the event publisher.
# Azure Event Hubs
- An Event Hubs namespace is a virtual server that you need to deploy in Azure to provision Event Hubs, a namespace works as a logical container for hubs and provides isolation and management feature for access control.
- An event-based processing service hosted in Azure. Event Hubs is designed to implement the classic publisher-subscriber pattern, where publishers generate millions of events that need to be processed and temporarily stored unless the consuming services can pull those events.
- Ingress connections are made by services (publishers) to produce the event streams.
- Egress connection are used by services (consumers) to receive the events for further processing.
- The events received from Event Hubs can be transformed, persisted, and analyzed with streaming solution and big data storage.
- Logs can be forwarded from the source and ingested into external Security Information and Event Management (SIEM) tools.
- Partition numbers can also affect the performance of Event Hubs. The idea of partitions is the parallel processing of events individually on each of the partitions. In Event Hubs, the partition represents the logically separated queue, where the event is stored until it's pulled by the client.
- Event Hubs receives events from publishers and persists event based on the retention period before consumers pull them. All events received by Event Hubs can be copied to the buffer and then flashed to the blob. If an event is received or deleted because of the expiration time, its copy still exists in the blob and is available for analysis.
- Consumer groups are used to perform an independent read of events by the application. Consumer groups represent the view of event's state in the partition for events consumers. Multiple readers of the same consumer group and the same partition can complicate event consumption and cause duplicates. The number of consumers should not exceed 5 consumers per group, per partition.
- The authentication and authorization of publishers and consumers are implemented based on SAS tokens generated with listen, send and manage rights.
- Azure Data Lake is used for storing captured events.
	- The charges for Azure Data Lake are less than the consumption charges from Event Grid per each of the event s and storing their content in Cosmos DB.
	- It is a consumption based service deployed as extension for an Azure storage account.
	- Capturing event content is impossible with Azure Monitor, Application Insights, or Azure Log Analytics workspace.
## Terminology
- Namespace is a management container for event hubs.
- Event publishers is any entity that sends data to an event hub.
- 
## Apache Kafka
- Azure Event Hubs provides an Apache Kafka endpoint on an event hub, which enables users to connect the event hub using the Kafka protocol. You can often use an event hub's Kafka endpoint from your applications without any code changes. You modify only the configuration, that is, update the connection string in configuration to point to a Kafka cluster. Then, you can start streaming events from your applications that use the Kafka protocol into event hubs, which are equivalent to Kafka topics.
- Similar concept:

  | Kafka concept | Event Hubs concept |
  | --------- | --------- |
  | Cluster | Namespace |
  | Topic | An event hub|
  | Partition | Partition |
  | Consumer group | Consumer group |
  | Offset | Offset |
- Schema:
	- Azure Schema Registry is a feature of Event Hubs, which provides a central repository for schemas for event-driven and messaging-centric applications, helps producer and consumer exchange data without having to manage and share the schema.
# Consuming event streams with Azure IoT Hub
- Azure IoT Hub platform is very similar to Event Hubs.
- Designed for consuming streaming telemetry from IoT devices and also managing the devices that produce the streams.
# Azure Event Grid
- It implements a reactive programming model where a specific algorithm is triggered depending on the event processed on Event Grid.
- The significant difference is that Event Grid can duplicate an event for all registered subscribers. This means that events can be delivered to each of the services that subscribed to those events. This logic, when one data change is duplicated for all registered subscribers, is known as a topic.
- Event sources and handlers:
	- Event source or publisher: For Event Grid, this is one of the following services: Azure Blob storage, Azure Resource Manager, Event Hubs or IoT Hub, Azure Service Bus, Azure Media Services, Azure Maps, or another Azure PaaS service. Moreover, Event Grid can work as a message broker. Your application can submit custom events to the public endpoint, and they can be delivered to subscribers.
	- Event handlers or subscribers:The relationship between the event source and the event handler is implemented by an Event Grid topic. The endpoint that's used by the event source to send events is named the event topic. On the other hand, the event subscription is a record that contains event handler registration, the kind of topic it subscribes to, and the type of event it wants to receive.
- Schema formats:
	- Default: presented in JSON format.
	- CloudEvent: maintained for multi-cloud collaboration.
- The authentication and authorization process in Event Grid is handled by Azure AD and access keys or SAS keys.
- Event Grid event domain is a logical structure that helps manage a high number of event recipients and also provides security isolation for subscribers by managing authorization and authentication settings.
- While event are being delivered by Event Grid, errors can occur and postpone or prevent delivery. The Event Grid will retry a number of times to delivery events, you can specify the dead-letter container on Azure Storage to persist the events that are not delivered after the retries.
## Use cases
- MQTT (message queuing telemetry transport) messaging.
- Ingest IoT telemetry: using a many to one messaging pattern for enabling the application to offload the burden of managing the high number of connections with devices to Event Grid.
- Command and control: using request-response (1 to 1) to send a command from a cloud application to an IoT device.
- Broadcast alerts: using one-to-many messaging pattern to publish only 1 message that the service replicates for every interested client.
- Integrate MQTT data.
## Push delivery of discrete events
- Build event-driven serverless solutions.
- Receive events from Azure services.
- Receive events from your applications.
- Receive events from parter (SaaS provider)
## Pull delivery of discrete events
- Receive events at your own pace.
- Consume events over a private link.
# Compare services

| Service | Main Pattern | Pros | Cons | 
| ------- | ------ | ------ | ------ |
| Event Hubs| Big data ingestion and streaming | High-scale. Event persistence. Capturing events. Low-cost. |  An event can be received only once. Complex order support with multiple partitions. Server-managed cursor |
| IoT Hub | Telemetry steaming | Two-way communication. Device registration and settings management. Free Standard tier. | No flexible pricing models. Sophisticated integration devices with Azure IoT Edge.
| Notification Hubs | Event broadcasting | Multi-platform support. Free tier. | Lack of troubleshooting tools. A limited number of supported platforms. |
| Event Grid | Pub/Sub and reactive programming | Consumption-based pricing. Integration with other services. Topic support. | The event delivery sequence can be changed. |

# Notes
- You have an instance of Azure Event Grid. You need to ensure an application can receive events filtered by values in the advanced filtering options.
	- Use `advanced`. An advanced filter is used to filter events by values in the data fields and specify the comparison operator.
	- An event type filter is used to send only certain event types to the endpoint.
	- A subject filter is used to specify a starting or ending value for the subject.
	- Topics is not a type filter, the event grid topic provides an endpoint where the source send events.
- You create an Azure Service Bus topic with a default message time to live of 10 times. You need to send messages to this topic with a time to live of 15 mins. The solution must not affect other applications that are using the topic.
	- Use create a new topic with a default time to live of 15 mins. Send the message to this topic.
	- To avoid affecting existing applications, the time to live of the existing topic must not be change. A new topic needs to be created.
	- Changing the topic's default time to live will affect other applications.
	- A message-level time to live cannot be higher than the topic's time to live.
	- To avoid affecting existing applications, the time to live of the existing topic or queue must not be changed.