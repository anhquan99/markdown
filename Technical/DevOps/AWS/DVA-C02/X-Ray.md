- Debugging service of AWS.
# Concepts
- Segments: the compute resources running your application logic send data about their work as segments.
- Subsegments:
	- A segment can break down the data about the work done into subsegments.
	- Subsegment provide more granular timing information and details about downstream calls that your application made to fulfill the original request.
- Traces: track the path of a request through your application.
# Advantages
- Troubleshooting performance (bottlenecks).
- Understand dependencies in a microservice architecture.
- Pinpoint service issues.
- Review request behavior.
- Find errors and exceptions.
- SLA.
- Where throttle issue.
- Identify users that are impacted.
# Compatibility
- Lambda
- Elastic Beanstalk
- ECS
- ELB
- API Gateway
- EC2 or application server (even on premise)
# Tracing
- Tracing is an end to end way to following a request.
- Each component dealing with the request adds its own trace.
- Tracing is made of segments ( + sub segments).
- Annotations can be added to traces to provide extra-information.
- Ability trace:
	- Every request
	- Sample request (as a % for example or a rate per minute)
# Security
- IAM
- KMS for encryption
# Enable X-Ray
- Import the SDK
- Install the X-Ray daemon or enable X-Ray AWS integration
# Troubleshooting
- <mark style="background: #BBFABBA6;">If X-Ray is not working on EC2</mark>
	- Ensure the EC2 IAM Role has the proper permissions
	- Ensure the EC2 instance is running the X-Ray Daemon
- <mark style="background: #BBFABBA6;">AWS Lambda</mark>
	- Ensure IAM execution role with proper policy `AWSX-RayWriteOnlyAccess`
	- Ensure X-Ray is imported in the code
	- Enable **Lambda X-Ray Active Tracing**
# Instrumentation
- Means the measure of product's performance, diagnose errors, and to write trace information.
# Concepts
- <mark style="background: #ADCCFFA6;">Segments:</mark> each application / service will send them.
- <mark style="background: #ADCCFFA6;">Subsegments:</mark> if you need more details in your segment.
- <mark style="background: #ADCCFFA6;">Trace:</mark> segments collected together to form an end-to-end trace.
- <mark style="background: #ADCCFFA6;">Sampling:</mark> decrease the amount of requests sent to X-Ray, reduce cost.
- <mark style="background: #ADCCFFA6;">Annotations:</mark> key value pairs used to index traces and use with filters.
- <mark style="background: #ADCCFFA6;">Metadata: </mark>key value pairs not indexed, not used for searching.
- The X-Ray daemon / agent has a config to send traces cross account:
	- Make sure the IAM permissions are correct - the agent will assume the role.
	- This allows to have a central account for all your application tracing.
# Sampling rules
- You can control the amount of data that you record and modify sampling rules without changing your code.
- By default, the X-Ray SDK records the first request each second, and 5% of any additional request.
- 1 request per second is the reservoir, which ensures that at least 1 trace is recorded each second as long the service is serving the requests.
- 5% is the rate at which additional request beyond the reservoir are sampled.
# API
- `PutTraceSegments`: upload segment documents to X-Ray.
- `PutTelemetryRecords`: used by X-Ray daemon to upload telemetry.
- `GetSamplingRules`: retrieve all sampling rule (to know what/when to send).
- `GetSamplingTargets` and `GetSamplingStatisticSummaries`
- `GetServiceGraph`: main graph
- `BatchGetTraces`: retrieves a list of traces specified by ID. Each trace is a collection of segment documents that originates from a single request.
- `GetTraceSummaries`: retrieves IDs and annotations for traces available for a specified time frame using an optional filter. To get full traces, pass the trace IDs to `BatchGetTraces`.
- `GetTraceGraph`: retrieves a service graph for one or more specific trace IDs.
# With Elastic Beanstalk
- Run daemon by setting an option in `.ebextensions/xray-daemon.config`.
- <mark style="background: #FF5582A6;">Note:</mark> the X-Ray daemon is not provided for multi-container Docker.
# With ECS
## ECS cluster
- X-Ray container as a daemon.
- X-Ray container as a side-car
## Fargate
- Side-car
# Distro for OpenTelemetry
- Secure, production-ready AWS-supported distribution of the open-source OpenTelemetry project.
- <mark style="background: #FF5582A6;">Migrate from X-Ray to AWS Distro for Telemetry if you want to standardlize with open-source APIs from Telemetry or send traces to multiple destinations simultaneously.</mark>
