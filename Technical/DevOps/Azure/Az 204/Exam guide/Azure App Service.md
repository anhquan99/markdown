# App Service Environments (AESs)
- An App Service Environment is an Azure App Service feature that provides fully isolated environment for securely running apps when you need very high-scale, secure, isolated network access and high compute utilization.
- AESs are appropriate for application workloads:
	- High scale.
	- Isolation and secure network access.
	- High memory utilization.
	- High requests per second (RPS). You can create multiple AESs in a single Azure region or across multiple Azure regions. This flexibility makes an AES ideal for horizontally scaling stateless applications with a high RPS requirement.
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
- Types:
	- Application
		- Generated by application code.
		- Save in App service file system or Azure Storage blobs.
	- Web server
		- Raw HTTP request data.
		- Save in App service file system or Azure Storage blobs.
	- Detailed error messages
		- Copy of the HTML error pages that would have been sent to the client browser. Because of the security reason, the detail error should not send to client.
		- Save in App service file system.
	- Failed request tracing 
		- Detail tracing information on failed requests.
		- Save in App service file system.
	- Deployment
		- Logs for when you publish content to an app. Deployment logging happens automatically and there are no configurable settings for deployment settings.
		- Save in App service file system.
- The log saved in file system is for temporary debugging purposes, and turn itself off in 12 hours. The Azure Storage blob is for long-term logging.
- In the Linux /Container application logging, the there is an option Retention Period (days) which set the number of days the logs should be retained.
- Windows only:
	- Detailed error logging.
	- Failed request tracing.
	- Web server logging.
- Windows and Linux:
	- Application logging.
	- Deployment logging.
- Service monitoring overview:
	- Diagnostic Settings (vis Azure Monitor)
	- Quotas and alerts
	- Metrics
	- Activity logs
	- Application Insights (via Azure Monitor)
	- Log stream (via App Service Logs)

| Scenario | Monitoring method |
| --- | --- |
| Monitor platform metrics and logs | (Azure Monitor) Diagnostic Settings |
| Monitor application performance and usage | (Azure Monitor) Application Insights |
| Monitor built-in logs for testing and development | Log stream |
| Monitor resource limits and configure alerts | Quotas and alerts |
| Monitor web app resource events | Activity logs |
| Monitor metrics visually | Metrics |
# Custom domains
- CNAME stands for Canonical Name. It is a type of Domain Name System (DNS) record that maps an alias domain name to a canonical domain name. This means that when a user visits the alias domain name, they will be automatically redirected to the canonical domain name. Example:
	- Alias domain name `www.example.com` will be automatically redirected to `example.com`
- The TXT record is a domain verification ID that helps avoid subdomain takeovers from other App Service apps. For custom domains you previously configured without this verification ID, you should protect them from the same risk by adding the verification ID (the TXT record) to your DNS configuration.
# TLS/SSL certificates
- Add certificate:
	- Create App Service Manage Certificate.
	- Import App Service Certificate.
	- Upload a private or public certificate (.pfx).
		- Private certificate requirements:
			- Export as a password-protected PFX file, encrypted using triple DES.
			- Contains private key at least 2058 bits long.
			- Contains all intermediate certificate and the root certificate in the certificate chain.
	- Import from Key Vault.
- TLS/SSL type:
	- SNI SSL
		- Stands for Server Name Indication Secure Layer. It is an extension to the TLS protocol (formerly known as the SSL protocol) that allows a client device to specify the hostname it is trying to reach in the first step of the TLS handshake. This prevents common name mismatch errors, which can occur when a server hosts multiple websites on the same IP address and each website has its own SSL certificate.
		- SNI SSL works by adding a new field to the TLS Client Hello message, which is the first message sent by the client device during the TLS handshake. The field contains the hostname that the client device is trying to reach. The server then uses this information to select the appropriate SSL certificate to present to the client device.
		- Benefits:
			- Allows multiple websites to be hosted on the same IP address, each with its own SSL certificate.
			- Prevents common name mismatch errors, which can improve the user experience and reduce the risk of phishing attacks.
			- It improves the security of HTTPS connections by making it more difficult for attackers to intercept and decrypt traffic.
	- IP based SSL
- In App Service, TLS termination happens at the network load balancer, so all HTTPS requests reach your app as unencrypted HTTP requests, If your app logic needs to check the user requests are encrypted or not, inspect the `X-Forwared-Proto` header.
- You can restrict access to your Azure App Service app by enabling different types of authentication for it. One way to do it is to request a client certificate when the client request is over TLS/SSL and validate the certificate. This mechanism is called TLS mutual authentication or client certificate authentication.
	- In App Service, TLS termination of the request happens at the frontend load balancer. When forwarding the request to your app code with client certificates enabled, App Service inject an `X-ARR-ClientCert` request header with the client certificate. App Service does not do anything with this client certificate other than forwarding it to your app. Your app code is responsible for validating the client certificate.
# Database and service connection
- Connection methods:
	- Direct connection from App Service managed identity.
		- Best for enterprise-level security.
		- Large team or automated connection string and secret management.
		- Credentials aren't accessible to you.
		- Managed identities can be used without any additional cost.
	- Connect using Key Vault secrets from App Service managed identity.
		- Best for enterprise-level security.
		- Large team or automated connection string and secret management.
		- When managed identity isn't supported for your app's dependent services, use Key Vault to store your secrets, and connect your app to Key Vault with a managed identity.
	- Connect with app settings.
		- Small team or individual owner of Azure resources.
		- Security of connection information is manual and limited to a few people
		- Web app is temporary, proof-of-concept, or in first migration stage to Azure.
- Use managed identity to authenticate from one Azure resource to another Azure resource. This level of authentication lets Azure manage the authentication process, after the required setup is complete. Benefits:
	- Automated credentials' management.
	- Many Azure services are included.
	- No additional cost.
	- No code changes.
- Consideration for Azure Files mounting: 
	- Apps can use the `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` application setting to mount Azure Files as the file system. This setting has validation checks to ensure that the app can be properly started.
	- When you use key vault references in this setting, the validation check fails by default, because the secret itself can't be resolved while processing the incoming request. To avoid this issue, you can skip the validation by setting `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` to `1`. This setting tells App Service to bypass all checks, and doesn't create the content share for you. You should ensure that it's created in advance.
	- If you skip validation and either the connection string or content share are invalid, the app will be unable to start properly and will only server HTTP 500 errors.
- Consideration for Application Insights instrumentation:
	- App can use the `APPINSIGHTS_INSTRUMENTATIONKEY` or `APPLICATIONINSIGHTS_CONNECTION_STRING` application settings to integrate with Application Insights. If these values are referenced from Key Vault, these aren't available, and you need to work directly with the Application Insights resource to view the telemetry. However, these values are not considered secrets.
- REST endpoint reference
	- An app with a managed identity makes this endpoint available by defining 2 environment variables:
		- `IDNEITY_ENDPOINT`: the URL to the local token service.
		- `IDENTITY_HEADER`: a header used to help mitigate server-side request forgery (SSRF) attacks.
# Scaling and performance
- Scale up: scale the hardware of the App Service plan and changing the pricing tier it belongs to.
- Scale out: increase the number of VM instances that run your app.
- The scale settings take only seconds to apply and affect all apps in your App Service plan, and don't require changing your code or redeploy your application.
- **Automatic scaling**:
	- It is a new scale out option that automatically handles scaling decisions for your web apps and App Service Plans. It's different from the pre-existing **Azure autoscale**, which lets you define scaling rules based on schedules and resources. With automatic scaling, you can adjust scaling settings to improve your app's performance and avoid cold start issues. 
	- The platform prewarms instances to act as a buffer when scaling out, ensuring smooth performance transitions.
	- Automatic scaling doesn't use schedule-based scaling, it always has ready instances (minimum 1) and prewamed instances (default 1).
	- You enable automatic scaling for an App Service Plan and configure a range of instances for each of the web apps. As you web app starts receiving HTTP traffic, App Service monitors the load and adds instances.
	- Scenarios:
		- You don't want to set up autoscale rules based on resource metrics.
		- You want your web apps within the same App Service Plan to scale differently and independently of each other.
		- Your web app connected to a databases or legacy system, which may not scale as fast as the web app. Scaling automatically allows you to set the maximum number of instances your App Service Plan can scale to. This setting helps the web app to not overwhelm the backend.
- Flapping: refers to a loop condition that causes a series of opposing scale events. Flapping happens when one scale event triggers an opposite scale event. Example, scaling in reduces the number of instances causing the CPU to rise in the remaining instances. This in turn triggers scale out event, which causes CPU usage to drop, repeating the process.
# Notes
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