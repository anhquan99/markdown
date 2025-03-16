# Logstash
- Logstash is an open source event processing engine by configuring a pipeline which is responsible for receiving, processing and shipping events.
```ad-note
Pipeline = input + (filter) + ouput
```
- Logstash is using decoupled architecture, it helps centralized event processing.
- Logstash uses plugin to handle all processing data.
## Components
### Input
- Get data into Logstash.
### Filter
- Filters are intermediary processing devices in the Logstash pipeline. You can combine filters with conditionals to perform an action on an event if it meets certain criteria.
### Output
- Outputs are the final phase of the Logstash pipeline. An event can pass through multiple outputs, but once all output processing is complete, the event has finished its execution.
### Codecs
- Codecs are stream filters that can operate as part of an input or output. Codecs enable you to easily separate the transport of your messages from the serialization process.
## Basic
### Configuration
```
input {}
filter {}
output {}
```
### Configuration files
- `logstash.yml`: contains Logstash configuration flags. You can set flags in this file instead of passing the flags at the command line.
- `pipelines.yml`: contains the framework and instructions for running multiple pipelines in a single Logstash instance.
- `jvm.options`: contains JVM configuration flags. Use this file to set initial and maximum values for total heap space. You can also use this file to set the locale for Logstash. Specify each flag on a separate line. All other settings in this file are considered expert settings.
### Execution model
- Each input stage in the Logstash pipeline run its own thread. Inputs write events to a central queue that is either in memory (default) or on disk. Each pipeline worker thread takes a batch of events off this queue, runs the batch of events through the configured filters, and then runs the filtered events through any outputs.
- The size of the batch and number of pipeline worker threads are configurable.
- By default, Logstash uses **in-memory bounded queues** between pipeline stages (input → filter and filter → output) to buffer events. **If Logstash terminates unsafely, any events that are stored in memory will be lost. To help prevent data loss**, you can enable Logstash to persist in-flight events to disk - Persistent queues.
- A pipeline worker consumes events from the queue in batches, which optimize the processing of events to increase the throughput of the pipeline. 
- The batch size is determined by 2 configuration options:
	- **Maximum batch size**
	- **Batch delay**: how long to wait before processing an undersized batch of events
	- Example: max batch size is 100 and batch delay is 100 seconds. If a batch of events has 50 unprocessed events, after 100 seconds it will be processed even the batch size is not reached, Logstash wants to process event in timely manner and not keeping the events in the work queue for too long.
- Logstash inspects the number of CPU cores and starts up an appropriate number of pipeline workers based on that - utilize performance.
![Logstash execution model](/Image/elk-logstash-execute-model.excalidraw.png)
## Multiple pipelines
- Using multiple pipelines is especially useful if your current configuration has event flows that don’t share the same inputs/filters and outputs and are being separated from each other using tags and conditionals.
- Having multiple pipelines in a single instance also allows these event flows to have different performance and durability parameters (for example, different settings for pipeline workers and persistent queues). This separation means that a blocked output in one pipeline won’t exert backpressure in the other.
### Options
- Run multiple instances of Logstash.
- Run multiple pipeline in an instance:
	- Configured in file `pipelines.yml`
```yml
- pipeline.id: test
  pipeline.workers: 1
  pipeline.batch.size: 1
  config.string: "input { generator {} } filter { sleep { time => 1 } } output { stdout { codec => dots } }"
- pipeline.id: another_test
  queue.type: persisted
  path.config: "/tmp/logstash/*.config"
```
## Dead letter queue
- By default, when Logstash encounters an event that it cannot process because the data contains a mapping error or some other issue, the Logstash pipeline either hangs or drops the unsuccessful event.
- The dead letter queue (DLQ) is designed as a place to temporarily write events that cannot be processed. The DLQ gives you flexibility to investigate problematic events without blocking the pipeline or losing the events. Your pipeline keeps flowing, and the immediate problem is averted. But those events still need to be addressed.
![DLQ|277x270](/Image/image-12.png)