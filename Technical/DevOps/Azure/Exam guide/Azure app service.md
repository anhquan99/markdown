# App Service Environments (AESs)
- Provide fully isolated environment for securely running apps when you need very high-scale, secure, isolated network access and high compute utilization.
# Azure App Service Plan
- The concept of a server farm: a collection of powerful servers provide functionality beyond that of a single machine.
- An app service plan defines a set of resources that can be used by one or more applications. You can have multiple App Service applications with 1 App Service plan. Example: if your App Service plan has 5 VM instances, your application will run across all 5 of the instances.
- App Service Plan is the unit of scale for App Service applications.
# Authentication and authorization
- When enable authentication and authorization, these things will be handled:
	- Authenticates users with the identity provider.
	- Validates, stores and refreshes the tokens.
	- Manages the authenticated sessions.
	- Injects identity information into the request headers.
- On Windows App Service apps, the module runs as a native IIS module in the same sandbox as your application code. But in the Linux and container apps, the module run in a separate container, isolated from your code, because the module doesn't run in-process, there's no direct integration with specific language frameworks.
- Without provider SDK:
	- Server flow.
	- Login steps:
		1. Sign user in: Redirect client to `/.auth.login/<provider>`.
		2. Post authentication: Provider redirects client to `/.auth/<provider>/callback`.
		3. Establish authenticated session: App Service adds authenticated cookie to response.
		4. Serve authenticated content: Client includes authenticated cookie in subsequent requests.
- With provider SDK:
	- Client flow.
	- Login steps:
		1. Sign user in: Client code signs user in directly with SDK and receives authenticated token.
		2. Post authentication: Client code posts token to `/.auth/login/<provider>`.
		3. Establish authenticated session: App Service returns its own authentication token to client code.
		4. Serve authenticated content: Client code presents authentication token in X-ZUMO-AUTH header.
# Networking features
- Outbound flows:
	- App Service roles that host out workload are called workers, the roles that handle incoming request are called frontends. With Free and Shared App Service plans use multitenant workers (the same worker VMs will be running multiple customer workloads). Other plans will run on workers that are dedicated to single App Service plan. This lead to when you scale your App Service to the dedicated plan the outbound IP will be changed.
- Inbound flows:
	- Each App Service will just have a single inbound IP.
	- Features:
		- Assign dedicated IP with SSL.
		- Access restrictions.
		- Private endpoint feature, allows private and secure inbound connections to your app via Azure private link.