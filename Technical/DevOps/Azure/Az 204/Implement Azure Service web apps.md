# Support service
- Built-in auto-scale support: depending on the usage of the web app, the web service itself can scale up/down or scale out/in.
- CI/CD
- Deployment slots
- App Service on Linux
- Limitations:
	- App Service on Linux is not supported on Shared pricing tier.
# App Service plan defines:
- OS
- Region
- Number of VM instances
- Size of VM instances
- Pricing tier (Free, Shared, Basic, Standard, Premium, PremiumV2, PremiumV3, Isolated, IsolatedV2)
# Runs and scales (except for Free and Shared tier)
- An app runs on all the VM instances configured in the App Service plan.
- If multiple apps are in the same App Service plan, they all share the same VM instances.
- If you have multiple deployment slots for an app, all deployment slots also run on the same VM instances.
- If you enable diagnostic logs, perform backups, or run WebJobs, they also use CPU cycles and memory on these VM instances.
# Deployment
- Automated deployment
- Manual deployment
# Built-in authentication
- How it works?
	- Authenticates users and clients with the specified identity provider(s)
	- Validates, stores, and refreshes OAuth tokens issued by the configured identity provider(s)
	- Manages the authenticated session
	- Injects identity information into HTTP request headers
# Configure application settings
- List of available settings:
	- Stack settings: the software stack to run the app.
	- Platform settings: config hosting platform
		- Bitness: 32 or 64 bit.
		- WebSocket protocol.
		- Always on: keep app loaded even when there's no traffic.
		- Managed pipeline version: IIS pipeline mode.
		- HTTP version.
		- ARR affinity: In a multi-instance deployment, ensure the client is routed to the same instance for the life of the session.
	- Debugging: enable remote debugging.
	- Incoming client certificates.
# Diagnostic logging
- Windows supports logging:
	- Application.
	- Web server.
	- Detailed.
	- Failed request tracing.
	- Deployment.
- Linux supports logging:
	- Application.
	- Deployment.