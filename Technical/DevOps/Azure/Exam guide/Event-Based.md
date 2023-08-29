## Role of event-driven solutions
- **Event:** a small piece of valuable data that contains information about what is happening. Usually, the event body is represented in JSON format.
- **Event source or publisher:** the services where the event takes place.
- **Event consumer, subscriber, or handler:** the services that receive the event for processing or react to the event.
- **Event schema:** the key-value pairs that must be provided in the event body by the event publisher.
## Azure Event Hubs
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
- 