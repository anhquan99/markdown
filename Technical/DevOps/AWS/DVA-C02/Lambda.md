- [[Azure Functions]] alike.
# Benefits
- Easy pricing: pay per request and compute time.
- Integrated with the whole AWS suite of services.
- Integrated with many programming languages.
- Easy monitoring through AWS CloudWatch.
- Easy to get more resources per functions (up to 10GB of RAM).
- Increasing RAM will also improve CPU and network.
# Language support
- Can use Custom Runtime API (community supported, example Rust).
- Container image
	- The container image must implement the Lambda runtime API.
	- ECS / Fargate is preferred for running arbitrary Docker images.
# Integration with ALB
- To expose a Lambda function as an HTTP(s) endpoint.
- The Lambda function must be registered in a target group.
- ALB to Lambda = HTTP to JSON and reverse.
- When you enable multi-value headers, HTTP headers and query string parameters that are sent with multiple values are shown as arrays within the AWS Lambda event and response objects.
# Synchronous invocations
- User invoked
	-  ELB
	- API Gateway
	- CloudFront (Lambda@Edge)
	- S3 Batch
- Service invoked
	- Amazon Cognito
	- AWS Step Functions
# Asynchronous invocations
- The events are placed in an EventQueue.
- Lambda attempts to retry on errors.
	- 3 tries total
	- 1 min wait after 1st then 2 mins wait.
- Make sure the processing is **idempotent** (in case of retries).
- If the function is retried, you will see duplicate logs entries in CW Logs.
- Can define a DLQ - SNS or SQS for failed processing.
- Asynchronous invocations allow you to speed up the processing if you don't need to wait for the result.
## Services
- S3
- SNS
- EventBridge
- CodeCommit
- CodePipeline
# S3 events notifications
- If 2 writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent.
- If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket.
# Event source mapping
## <mark style="background: #BBFABBA6;">Kinesis data streams</mark>
- An event source mapping creates an iterator for each shard, processes items in order.
- Start with new item, from the beginning or from timestamp.
- Processed item aren't removed from the stream (other consumers can read them).
- Low traffic: use batch window to accumulate records before processing.
- You can process multiple batches in parallel.
- By default, if your function returns an error, the entire batch is reprocessed until the function succeeds, or the items in the batch expire.
- To ensure in-order processing, processing for the affected shard is paused until the error is resolved.
- You can configure the event source mapping to:
	- Discard old events
	- Restrict the number of retries
	- Split the batch on error (to work around lambda timeout issues)
	- Discarded event can go to a destination (where failed event can go to).
## <mark style="background: #BBFABBA6;">SQS and SQS FIFO</mark>
- Event source mapping will pool SQS (long polling) and specify batch size (1-10 messages).
- Recommended set the queue visibility timeout t 6x the timeout of your Lambda function, can use with DQL.
## <mark style="background: #BBFABBA6;">Queue</mark>
- Lambda supports in-order processing for FIFO queues, scaling up to the number of active **message groups**.
- Lambda scales up to process a standard queue as quickly as possible.
- When an error occurs, batches are returned to the queue as individual items and might be processed in a different grouping than the original batch.
- Occasionally, the event source mapping might receive the same item from the queue twice, even if no function error occurred.
# Event and context objects
## Event object
- JSON formatted document contains data for the function to process.
- Contains information from the invoking service.
- Lambda runtime converts the event to an object.
## Context object
- Provides methods and properties that provide information about the invocation, function, and runtime env.
- Passed to your function by Lambda at runtime.
# Execution role (IAM role)
- When you use an event source mapping to invoke your function, Lambda uses the execution role to read event data.
- Best practice: create 1 Lambda function role per function.
# Tracing with X-Ray
- IAM execution role `AWSXRayDaemonWriteAccess`.
- Env variables to communicate with X-Ray:
	- `_X_AMZN_TRACE_ID`: contains the tracing header
	- `AWS_XRAY_CONTEXT_MISSING`: by default LOG_ERROR
	- `AWS_XRAY_DAEMON_ADDRESSS`: the X-Ray Daemon IP_ADDRESS:PORT
# Customization at the edge
- Edge function:
	- A code that you write and attach to CloudFront distributions
	- Runs close to your users to minimize latency
- Used for customize the CDN content.
## <mark style="background: #BBFABBA6;">CloudFront functions</mark>
- Lightweight functions written in JS.
- Sub-ms- startup times, millions of request/second.
- Used to change Viewer request and responses:
	- Viewer request: after CloudFront receives a request from a viewer.
	- Viewer response: before CloudFront forwards the response to the viewer.
- Native feature of CloudFront.
## <mark style="background: #BBFABBA6;">Lambda@Edge</mark>
- Written in NodeJS or Python.
- Scales to 1000s of requests/second.
- Used to change CloudFront requests and responses:
	- Viewer request.
	- Origin requests: before CloudFront forward the request to the origin.
	- Origin response: after CloudFront receives the response from the origin.
	- Viewer response.

| CloudFront Functions | Lambda@Edge |
| -- | -- |
|Cache key normalization | Longer execution time (several ms) |
| Header manipulation | Adjustable CPI or memory | 
| URL rewrites or redirects | Your code depends on a 3rd libraries |
| Request authentication and authorization | Network access to use external services for processing |
| | File system access or access. to the body of HTTP requests |
# With VPC
 - You must define the VPC ID, the subnets and the security groups.
 - Lambda will create an ENI in your subnets.
 - Deploy a Lambda function in a public subnet does not give it internet access or a public IP, but in a private subnet gives it internet access if you have a NAT Gateway/Instance.
# Configuration
- The more RAM you add, the more vCPU credits you get.
- If your application is CPU-bound (computation heavy), increase RAM.
- Timeout: default 3 secs, max 900 secs (15 mins).
# Execution context
- Is a temporary runtime env that initialize any external dependencies of your lambda code.
- Great for db connections, HTTP clients, SDK clients, ...
- The execution context is maintained for some time in anticipation of another Lambda function invocation.
- The next function invocation can re-use the context tot execution time and save time connection objects.
- The execution context includes the `/tmp` directory.
## `/tmp` space
- Max size is 10GB.
- The directory content remains when the execution context is frozen, providing a transient cache that can be used for multiple invocation (checkpoint).
- To encrypt content you must generate KMS Data keys.
# Layers 
- Externalize dependencies to re-use them (docker onion layer architecture alike).
- Custom runtimes.