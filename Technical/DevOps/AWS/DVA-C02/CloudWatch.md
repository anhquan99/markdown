- Short call is CW.
- Provides metrics for every service in AWS.
- Metric is a variable to monitor, and it belongs to namespaces.
- Dimension is an attribute of a metric (instance id, env, ...).
- Up to 30 dimensions per metric.
- Metrics have **timestamps**.
# EC2 detailed monitoring
- EC2 instance metrics have metrics every 5 mins.
- With detailed monitoring (for a cost), you get data every 1 min which you can scale ASG faster.
- Free tier allows user to have 10 detailed monitoring metrics.
- Note: EC2 Memory usage is by default not pushed (must be pushed from inside the instance as a custom metric).
- You need to run a CW agent on EC2 to push the log files you want and make sure IAM permissions are correct.
## Agent
- CW Logs Agent
	- Old version of the agent
	- Can only send to CW Logs
- CW Unified Agent
	- Collect additional system-level metrics (CPU, Disk metrics, RAM, Netstat, Processes, Swap Space)
	- Collect logs to send to CW Logs
	- Centralized config using SSM Parameter Store
# Custom metrics
- Define and send your own custom metrics to CW.
- Use API call `PutMetricData`.
- Metric resolution (`StorageResolution` API parameter) :
	- Standard: 1 min
	- High Resolution: 1/5/10/30 secs - higher cost
- Important: accepts metric data points 2 weeks in the past and 2 hours in the future (make sure to config your EC2 instance time correctly).
# Logs
- **Log groups:** arbitrary name, usually representing an application.
- **Log stream:** instances within application / log files / containers.
- Encrypted by default.
- Can set up KMS with your own keys.
- CW Logs can send to:
	- S3
	- Kinesis Data Streams
	- Kinesis Data Firehose
	- AWS Lambda
	- OpenSearch
# Logs Insights
- Search and analyze log data stored in CW Logs.
- Provides a purpose-built query language.
- Can query multiple Log Groups in different AWS accounts.
- It's a query engine, not a real-time engine.
# Log Subscriptions
- Get a real-time log events from CW Logs for processing and analysis.
- Use **Subscription Filter** to filter which logs or events deliver to your destination.
- Cross-Account Subscription: send log events to resources in a different AWS account.
# Metric filter
- Filter do not retroactively filter data. Filters only publish the metric data points for events that happen after the filter was created.
- Ability to specify up to 3 Dimensions for the Metric Filter.
# Alarms
- Used to trigger notifications for any metric.
- States:
	- OK
	- INSUFFICIENT_DATA
	- ALARM
- Period:
	- Length of time in seconds to evaluate the metric.
	- High resolution custom metric: 10 or 30 seconds. You can set a regular alarm with a period of any multiple of 60 seconds.
## Targets
- Stop, Terminate, Reboot, or Recovery an EC2 instance.
- Trigger Auto Scaling Action.
- Send notification to SNS.
## Composite alarms
- Monitor the states of multiple other alarms.
- Use `AND` and `OR` conditions.
# Synthetics canary
- Configurable script that monitor your APIs, URLs, Websites, ...
- Reproduce what your customers do programmatically to find issues before customers are impacted.
- Checks the availability and latency of your endpoints and can store load time data and screenshots of the UI.
- Integration with CW Alarms.
- Can run once or on a regular schedule.
# EventBridge
- Former CloudWatch Event.
- Sent event to another service.
- You can archive events (all / filter) sent to an event bus (indefinitely or set period).
- Ability to replay archived events.
## Event buses
- An event bus is a router that receive events and delivers them to destinations, or targets.
  ![[Pasted image 20240306213727.png]]
  - You can archive, or save, events and then replay events at a later time from the archive.
  - Default event bus in AWS account only allows events from 1 account, you can attach policies to an IAM role to grant permission to send events to a different account or Region.
## Event
- An event indicates a change in an environment.
### Structure
- version
- id
- detail-type
- source
- account
- time
- region
- resource
- detail
## Pipe
- A pipe routes events from a single source to a single target. The pipe also includes the ability to filter for specific events, and to perform enrichments on the event data before it is sent to the target.
  ![[Pasted image 20240306222039.png]]
## Trigger
- Schedule: Cron jobs.
- Event pattern: event rules to react to a service doing something.
- Trigger Lambda functions.
## Schema registry
- EventBridge can analyze the events in your bus and infer the schema.
-  The schema registry allows you to generate code for your application, that will know in advance how data is structured in the event bus.
- Schema can be versioned.
# Evidently