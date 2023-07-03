- Azure functions is a serverless solution allows you to write less code, maintain less infrastructure, react to a series of critical events and supports triggers to execute code if an event happens.
- Azure functions is a serverless compute service, develop orchestrations by writing code and using the Durable Functions extension.
# Hosting plan
- Consumption plan.
- Premium plan: automatically scales based on demand using pre-warmed workers.
- Dedicated plan: run functions within an App Service plan, best for long-running scenarios where Durable Functions can't be used.
- ASE (App Service Environment): is an App Service feature fully isolated and dedicated environment for securely at high scale.
- Kubernetes: run on top of Kubernetes platform.
# Function app time duration
- The `functionTimeout` property in the host.json project file specifies the timeout duration for function app.
# Scale Azure functions
- Runtime scaling:
	- The scale controller monitors the rate of events and determine whether to scale out or scale in.
	- When is no functions are running, the number of instances is "scale in" to 0. If scale out from 0 instance the time of response will take longer, this is referred as cold start.
- Scaling behavior
	- Maximum instances.
	- New instance rate.