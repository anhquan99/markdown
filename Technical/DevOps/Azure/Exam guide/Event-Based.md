## Role of event-driven solutions
- **Event:** a small piece of valuable data that contains information about what is happening. Usually, the event body is represented in JSON format.
- **Event source or publisher:** the services where the event takes place.
- **Event consumer, subscriber, or handler:** the services that receive the event for processing or react to the event.
- **Event schema:** the key-value pairs that must be provided in the event body by the event publisher.
## Azure Event Hubs
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
## Consuming event streams with Azure IoT Hub
- Azure IoT Hub platform is very similar to Event Hubs.
- Designed for consuming streaming telemetry from IoT devices and also managing the devices that produce the streams.
## Azure Event Grid
- It implements a reactive programming model where a specific algorithm is triggered depending on the event processed on Event Grid.
- The significant difference is that Event Grid can duplicate an event for all registered subscribers. This means that events can be delivered to each of the services that subscribed to those events. This logic, when one data change is duplicated for all registered subscribers, is known as a topic.
- Event sources and handlers:
	- Event source or publisher: For Event Grid, this is one of the following services: Azure Blob storage, Azure Resource Manager, Event Hubs or IoT Hub, Azure Service Bus, Azure Media Services, Azure Maps, or another Azure PaaS service. Moreover, Event Grid can work as a message broker. Your application can submit custom events to the public endpoint, and they can be delivered to subscribers.
	- Event handlers or subscribers:
	- The relationship between the event source and the event handler is implemented by an Event Grid topic. The endpoint that's used by the event source to send events is named the event topic. On the other hand, the event subscription is a record that contains event handler registration, the kind of topic it subscribes to, and the type of event it wants to receive.
- Schema formats:
	- Default: presented in JSON format.
	- CloudEvent: maintained for multi-cloud collaboration.
- The authentication and authorization process in Event Grid is handled by Azure AD and access keys or SAS keys.
- Event Grid event domain is a logical structure that helps manage a high number of event recipients and also provides security isolation for subscribers by managing authorization and authentication settings.
- While event are being delivered by Event Grid, errors can occur and postpone or prevent delivery. The Event Grid will retry a number of times to delivery events, you can specify the dead-letter container on Azure Storage to persist the events that are not delivered after the retries.

| Service | Main Pattern | Pros | Cons | 
| ------- | ------ | ------ | ------ |
| Event Hubs| Big data ingestion and streaming | High-scale. Event persistence. Capturing events. Low-cost. |  An event can be received only once. Complex order support with multiple partitions. Server-managed cursor |
| IoT Hub | Telemetry steaming | Two-way communication. Device registration and settings management. Free Standard tier. | No flexible pricing models. Sophisticated integration devices with Azure IoT Edge.
| Notification Hubs | Event broadcasting | Multi-platform support. Free tier. | Lack of troubleshooting tools. A limited number of supported platforms. |
| Event Grid | Pub/Sub and reactive programming | Consumption-based pricing. Integration with other services. Topic support. | The event delivery sequence can be changed. |