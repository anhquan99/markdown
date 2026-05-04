# Events
- **Argo Events** is an event-driven workflow automation framework for Kubernetes which helps you trigger K8s objects, Argo Workflows, Serverless workloads, etc. on events from a variety of sources like webhooks, S3, schedules, messaging queues, gcp pubsub, sns, sqs, etc.
## Architecture
![](/Image/Pasted%20image%2020260412113213.png)
### EventSource
- An event source defines the configurations required to consume events from external sources and then transforms the events into cloud event and dispatches them over to the event bus.
### EventBus
- Acts as the transport layer of Argo events by connecting the event sources and sensors.
- Event sources publish the event while the sensors subscribe to the events to execute triggers.
- Implementation of event bus: Kafka, Jetstream, ...
### Sensor
- Defines a set of event dependencies (input) and triggers (outputs). It listens to events on the event bus and acts as an event dependency manager to resolve and execute the triggers.
### Trigger
- A trigger is the resource/workload executed by the sensor once the event dependencies are resolve.
### Workflow
1. An external system or system service generates an event, the event source component catches this event and convert it into the CloudEvents format.
2. The standard event format is sent to event bus.
3. The sensor, which is subscribed to the event bus, detect the event and check if the event satisfies it predefined dependencies.
4. Sensor executes the defined trigger.