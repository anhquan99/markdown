- [[Azure Functions]] alike.
# Benefits
- Easy pricing: pay per request and compute time.
- Integrated with the whole AWS suite of services.
- Integrated with many programming languages.
- Easy monitoring through AWS CloudWatch.
- Easy to get more resources per functions (up to 10GB of RAM).
- Increasing RAM will also improve CPU and network.
# Concept
- Event: is a JSON formatted document that contains data for Lambda function to process.
- Qualifier: when you invoke or view a function, you can include a qualifier to specify a version or alias.
# Lifecycle
![[Pasted image 20240307180005.png]]
# Deployment package
- Container images.
- .zip file archives.
- Layers
- If your deployment package is larger than 50 MB, upload your function code and dependencies to a S3 bucket.
# IaC tools
- [[CloudFormation]]
- [[Serverless Application Model (SAM)]]
- [[Cloud Development Kit (CDK)]]
# Private networking
- To connect to another AWS service, you can use VPC endpoints for private communication between your VPC and supported AWS service. Alternative approach is to use a NAT gateway to route outbound traffic to another AWS service.
- When you connect a function to a VPC, Lambda assigns your function to a Hyperplane ENI (elastic network interface) for each subnet in your function's VPC configuration. Lambda creates a Hyperplane ENI the first time a unique subnet and security group combination is defined for a VPC-enabled function in an account.
- To connect to a VPC, a function must have the following permissions:
	- Execution role permissions:
		- `ec2:CreateNetworkInterface`
		- `ec2:DescribeNetworkInterfaces`
		- `ec2:DeleteNetworkInterface`
		- `ec2:AssignPrivateIpAddresses`
		- `ec2:UnassignPrivateIpAddresses`
	- User permission:
		- `ec2:DescribeSecurityGroups`
		- `ec2:DescribeSubnets`
		- `ec2:DescribeVpcs`
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
## Concurrency
- By default, an account can provision 1000 concurrent executions across all functions in an AWS region.
- Concurrency is the number of in-flight requests that your function is handling at the same time.
- Formula to calculate the concurrency: $$Concurrency = (Average Requests Per Second) * (Average Request Duration In Seconds) $$
- Each instance of your execution env can handle at most 10 requests per second.
- **Reserved concurrency:** reserve a portion of your account's concurrency for a function to avoid other functions taking up all the available unreserved concurrency.
	- Your function can scale independently of other functions in your account.
	- Your function can't scale out of control.
	- You may not be able to use all of your account's available concurrency.
	  ![[Pasted image 20240307223514.png]]
- **Provisioned concurrency:** pre-initialize a number of env instance for a function to avoid cold start.
	- Lambda can provision between 500 and 3000 execution envs at once, depending on the region.
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
- Is a Lambda resource that reads from an event source and invokes a Lambda function.
## <mark style="background: #BBFABBA6;">Kinesis DynamoDB Streams</mark>
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
- Recommended set the queue visibility timeout 6x the timeout of your Lambda function, can use with DQL.
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

| CloudFront Functions                     | Lambda@Edge                                                |
| ---------------------------------------- | ---------------------------------------------------------- |
| Cache key normalization                  | Longer execution time (several ms)                         |
| Header manipulation                      | Adjustable CPU or memory                                   |
| URL rewrites or redirects                | Your code depends on a 3rd libraries                       |
| Request authentication and authorization | Network access to use external services for processing     |
|                                          | File system access or access. to the body of HTTP requests |
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
## Considerations
- To reduce the size of your deployment packages.
- To separate core function logic from dependencies.
- To share dependencies across multiple functions.
- To use the Lambda console code editor.
# File system mounting
- Lambda functions can access EFS file system if they are running in a VPC.
- Configure Lambda to mount EFS file system to local directory during initialization.
- Limitations: watch out for the EFS connection limits.
# Concurrency and throttling
- Concurrency limit: up to 1000 concurrent executions.
- Can set a reversed concurrency at the function level, which is the max number of concurrent instances that you want to allocate to your function.
- Throttle behavior:
	- Sync return `ThrottleError` - 429
	- Async retry and go to DLQ
- Provisioned concurrency:
	- Allocated before the function is invoked (in advance) to avoid cold start happens, and all invocations have low latency.
	- Application Auto Scaling can manage concurrency.
# Function dependencies
- You need to install packages alongside your code and zip it together.
- Upload the zip to Lambda if less than 50MB, else to S3.
# Wit CloudFormation
- Simple inline functions and it can not include function dependencies.
- Use `Code.ZipFile` property.
# S3
- You must store the Lambda zip in S3 and refer S3 zip location in the CloudFormation code.
- If you update the code in S3, but don't update S3Bucket, S3Key or S3ObjectVersion, CloudFormation will not update your function.
# With container images
- Deploy Lambda function as container images up to 10GB from ECR.
- Pack complex dependencies, large dependencies in a container.
- Test the containers locally using the Lambda Runtime Interface Emulator.
- Unified workflow to build apps.
## Best practice for optimizing container images
- **Use AWS-provided base images**.
- **Use multi-stage build:** build your code in larger preliminary images, copy only the artifacts you need in your final container image, discard the preliminary steps.
- **Build from stable to frequently changing:** take advantages of onion architecture in docker.
- **Use a single repository for functions with large layers.**
# Aliases
- They are pointers to Lambda function versions.
- Aliases are mutable, have their own ARNs and can not reference aliases.
- Enable canary deployment by assigning weights to lambda functions or stable configuration of your event triggers / destinations.
> [!info] Notes
> You can't publish your function and create a new version if the unpublished version ($LATEST) is the same as the previous published version. You need to deploy code changes or make updates to your function's environment variables in $LATEST before you can create a new version.


# With CodeDeploy
- CodeDeploy can help you automate traffic shift for Lambda aliases.
- The feature is integrated with the SAM framework.
- Can create Pre and Post traffic hooks to check the health of the Lambda function.
## Types:
- **Linear**: grow traffic every N minutes until 100%.
- **Canary**: try X percent then 100%.
- **AllAtOnce**: immediate.
## AppSpec.yml
- Name
- Alias
- CurrentVersion
- TargetVersion
# Function URL
- `https://<url-id>.lambda-url.<region>.on.aws`
- Can be applied to any function alias or to $LATEST.
## Security
- Resource-based Policies
	- Authorize other account / specific CIDR / IAM principals.
- CORS
- `AuthTye NONE` - allow public and authenticated access.
- `AuthTye AWS_IAM` - IAM is used to authenticate and authorize requests.
	- Both Principal's Identity-based Policy and Resource-based Policy are evaluated.
	- Principal must have `lambda:InvokeFunctionUrl` permissions.
	- **Same account** = Identity-based policy **OR** Resource-based Policy as ALLOW.
	- **Cross account** = Identity-based policy **AND** Resource-based Policy as ALLOW.
# CodeGuru profiling
- Gain insights runtime performance of your Lambda functions using CodeGuru Profiler.
- CodeGuru creates a Profiler Group for your Lambda function.
- Support Java and Python runtimes.
# Best practice
- Perform heavy-duty work outside of your function handler
- Use env variables for
	- DB connection string, S3, ...
	- Passwords, sensitive values, ...
- Minimize your deployment package size to its runtime necessities.
- Avoid using recursive code, never have a Lambda function call itself.
## Function code
- Separate the Lambda handler from your core logic.
- Take advantage of execution env reuse to improve performance of your function.
- Use a keep-alive directive to maintain persistent connections.
- Use env variables to pass operational parameters to your function.
- Control the dependencies in your function's deployment package.
- Minimize your deployment package size to its runtime necessities.
- Reduce the time it takes Lambda to unpack deployment packages.
- Minimize the complexity of your dependencies.
- Avoid using recursive code.
- Do not use non-documented, non-public APIs.
- Write idempotent code: ensure duplicate events are handled the same way.
## Function configuration
- Performance testing your Lambda function: any increase in memory size triggers an increase in CPU.
- Load test your Lambda function to determine an optimum timeout value.
- Use most-restrictive permissions when setting IAM policies.
- Be familiar with your Lambda quotas.
- Be familiar with your upstream and downstream throughput constraints.
- Delete Lambda function that you are no longer using.
## Notes
- Environment variables max size = 4KB.