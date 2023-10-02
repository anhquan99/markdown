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
		1. Sign user in: Redirect client to `/.auth/login/<provider>`.
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
	- App Service roles that host out workload are called workers, the roles that handle incoming request are called frontends. With Free and Shared App Service plans use multi tenant workers (the same worker VMs will be running multiple customer workloads). Other plans will run on workers that are dedicated to single App Service plan. This lead to when you scale your App Service to the dedicated plan the outbound IP will be changed.
- Inbound flows:
	- Each App Service will just have a single inbound IP.
	- Features:
		- Assign dedicated IP with SSL.
		- Access restrictions.
		- Private endpoint feature, allows private and secure inbound connections to your app via Azure private link.
# Logging
- Windows only:
	- Detailed error logging.
	- Failed request tracing.
	- Web server logging.
- Windows and Linux:
	- Application logging.
	- Deployment logging.
## Notes
- To load a TLS/SSL certificate in a Windows custom container application, use `WEBSITE_LOAD_CERTIFICATES` configuration.
- You create an Azure web app locally. The web app consists of a ZIP package. You need to deploy the web app by using the Azure CLI. The deployment must reduce the likelihood of locked files.
	- Run `az webapp deploy`
	- Using a production and staging slot with auto swap enabled reduces the likelihood of locked files. If `-clean true` is used, the target folder is cleaned, but this has no effect on the likelihood of locked files. It is good to restart the app after deployment. This, however, is the default behavior of a ZIP deployment and has no effect on the reduced likelihood of locked files during deployment.
- You manage a multi-instance deployment of an Azure App Service web app. You need to ensure a client application is routed to the same instance for the life of the session.
	- **ARR Affinity**
	- In a multi-instance deployment, the ARR Affinity setting ensures a client application is routed to the same instance for the life of the session. WebSocket is a standardized protocol that provides full-duplex communication. Always on keeps the app loaded even when there is no traffic. In HTTP/2, a persistent connection can be used to service multiple simultaneous request.
- You develop an App Service hosted on Windows platform. Users report that the app is failing. You need to begin troubleshooting the app by inspecting a copy of the page that is returned when the HTTP return code is greater than 400.
	- Review **detailed error** log.
	- The detailed error log contains copies of the error pages, produced in response to HTTP codes greater than 400, that would have been sent to clients. These pages are not sent due to security reasons. 
	- The web server log shows information about the raw HTTP request, such as method, bytes, and client user agent.
	- The application log is application specific, logging information that your application code or components that are used by your application writes. The deployment log stores information to diagnose the reasons for a failed deployment.
- A company has an App Service web app that required a TLS/SSL certificate. The certificate will be used in other App Service apps. The certificate must be automatically renewed with the least management overhead. You need to add the certificate.
	- Use **Purchase an App Service certificate**.
	- Purchasing an App Service certificate automates the process of requesting, renewing, and synchronizing the certificate with the App Service apps that use them.
	- Free App Service certificates offer basic functionalities and cannot be exported.
	- Obtaining the certificate from a third party and uploading it to Azure App Service is also an option but lacks the automation and integration offered by the App Service certificates.
	- It's recommended to store certificates in and retrieve them from a Key Vault, but if they are obtained from a third party, the renewal and synchronization with the App Service apps need to be automated in other ways.