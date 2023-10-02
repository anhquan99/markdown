- Logic Apps development is more declarative, with a designer-first focus, and monitored using Azure portal and Azure Monitor logs. Whereas Azure Functions are more imperative, code first and monitored using Application Insights.
- App Service WebJobs and Azure Functions are built with the WebJobs SDK, both built on App Service. Azure Functions has several features that can offer developers more productivity than WebJobs:
	- A serverless application model with automatic scaling without additional configuration.
	- The ability to develop and test within the browser.
	- Trigger on HTTP/webhook and Azure Event Grid events.
	- More options for languages, development environments, pricing and integrations with Azure services.
	- Pay-per-use pricing.
# Scaling Azure Functions
- The number of instances that Azure Functions scale to is determined by the number of events that trigger a function.
- Function apps are the unit of deployment for Azure Functions and also are the unit of scale for Azure Functions. If a function app scales, all functions within the app scale at the same time.
- The scale controller monitors the rate of events to decide whether to scale in or out with different logic based on the type of trigger being used.
- Maximum 200 instances on Consumption plan and 100 on Premium plan.
# Triggers and bindings
- Triggers cause functions to run, and bindings are how you connect your function to other services.
- Input binding is the data your function receives, and output binding is the data your function sends.
# Develop Azure Functions
- The configuration file is created automatically for compiled languages based on annotations in the code, for scripting languages the configuration file needs to be created.
- Time functions use NCrontab syntax.
- Serverless functions are typically intended to be single purpose, short-lived and stateless, which is greate for scaling.
- Stateful durable functions:
	- Durable functions are an extension of Azure Function that allows you to write stateful, serverless workflows (orchestrations). They manage the state, checkpoints and restart for you, using data stored in the storage account to keep track of the orchestration process.
	- Types:
		- Client functions (starter functions): triggers and initializes a new orchestration workflow because you can't manually trigger an orchestrator function.
		- Orchestrator functions: define steps within a workflow and handle any errors that occur at any point in the workflow. They don't perform any activity, only orchestrate.
		- Activity functions: implement the steps within a workflow.
		- Entity functions (durable entities): define the operations for reading and updating small pieces of state and can be invoked from client functions or orchestrator functions, accessed via unique entity ID.
	- Patterns:
		- Function chaining: output from a function is passed to input of another function in order.
		- Fan-out/fan-in: multiple functions need to be executed in parallel, and progress needs to wait for all those functions to complete.
		- Asynchronous HTTP APIs: useful when coordination is required between long-running operations and external clients. Once the long-running operation starts, the orchestrator function manages polling the status until the operation completes or times out.
		- Monitor: the orchestrator function will cal an activity function that checks whether monitoring conditions are being met.
		- Human interaction.
		- Aggregator: used when data needs to be aggregated over a period of time into a single, addressable entity.
# Task hubs
- They are a logical container for storage resources used in durable functions orchestration such as the queues, tables, and containers we can see in our storage account.
# Controlling timing
- It used for timeout when functions run too long.
## Notes
- When using scripting languages, the function.json file for each function contains its triggers and bindings, and it needs to be explicitly created. The file host.json has runtime-specific configurations, not definitions of triggers and bindings. Decorating methods and Decorating parameters are used to define triggers and bindings when using compiled languages, not scripted ones.
- The fan-out/fan-in pattern enables multiple function to be executed in parallel, waiting for all functions to finish. Often, some aggregation work is done on the results that are returned from the functions.
- The function chaining pattern is a sequence of functions that execute in a specific order.
- Using the `dynamicThrottlesEnable` property allows developers to let the system respond dynamically to an increased utilization. This property is defined in the host.json file. The binding section, part of the function.json file, is used to define the bindings and triggers for function.
-  The `maxConcurrentRequests` property is used to determine the maximum number of function instances to run in parallel, it's defined in the function.json file.
- The `maxOutstandingRequest` property, defined in the host.json file, defines the maximum number of requests, queued or in progress, held at any given time.