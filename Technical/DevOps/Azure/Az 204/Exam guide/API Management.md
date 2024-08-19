## What is a web API?
- A web API is a well-known and widely used technology to transfer data through communication channels on the internet. Web APIs are usually implemented on servers as a group of endpoints and used for connections from a variety of clients. Endpoints support the Representational State Transfer (REST) interface, allowing the manipulation of data objects by using HTTP verbs. The data formats engaged in communication are commonly represented in JSON or XML format.
- Furthermore, the Open Data Protocol (OData) can be used to filter and summarize data chunks exposed by RESTful interfaces. Also, OData provides a bunch of guidelines and best practices for operating with REST interfaces. Following the guidelines is a plus for any application working with web APIs.
## API Management (APIM) services
- The biggest of APIM service is its various ways of configuration, including security management, products and subscription management and advanced policy configuration, which incorporates sophisticated algorithms of caching and throttling in the communication between clients and your web APIs.
- From an architecture view, the APIM service is designed as a facade service to protect the web APIs exposed.
## Configuration options
- Products and subscriptions
	- An API is a set of endpoints or operations available for calling by clients to receive data.
	- A product is a set of APIs groped logically.
	- A subscription is a key provided for a customer who wants to call APIs included in the product.
	- APIM can create several types of products:
		- Free product: requires subscription setting set to Off.
		- Billable product: requires a subscription to charge customers individually.
		- Trial product: limited access time during the trial period. APIM does not have handy settings to control the trial time, but in the access policies, you can verify the date when the subscription was created to control the length of the trial period.
- Authentication:
	- API operation calls are implemented based on the subscription based on the subscription key related to the products, additional authentication mechanisms can be implemented on advanced policy level, such as verification of the request source (IP addresses) and certificate verification.
- User account:
	- Admin group: full permission.
	- Developer group with authenticated users who can test APIs based on the visibility of the products and request subscription. Developers can get access to documentation and monitor the usage of the services of their applications.
	- Guests group: unauthenticated users who can observe APIs but not all.
- Networking:
	- APIM can be connected to a VNet in Azure and on-permises.
	- If you are going to use Azure VMs, you must be aware of port requirements that allow APIM to monitor the state of the backend and use Load Balancer.
- Dev portal:
	- Available for all pricing tiers except the Consumption tier.
	- The dev portal allows clients who want to integrate with provided APIs to observe its documentation, test, and manage its subscriptions.
- Self-hosted gateways:
	- Hybrid and multi-cloud companies need to run APIM instances in an isolated network. By deploying a self-hosted gateway, they can host a local APIM instance on-premises. 
	- The deployed gateway works as a proxy server and provides connectivity to registered Azure APIM resources for applications from the local environment. The self-hosted gateway is implemented as a Docker image. Then, it can be configured and managed from the Azure portal as a deployment with one or more nodes. 
	- Accounts are charged a fixed price per deployment, but their nodes run for free. If a company wants to provide access hosted on on-premises web API servers for an application running in Azure, it should consider using networking solutions such as a VPN or Azure Hybrid Connections.
- External cache:
	- These functions protect APIM from hammering by client requests.
	- The customer who owns the API could implement a throttling limit to prevent clients from calling the API often, or implement caching logic to cache the frontend or backend output.
- Repository integration
- Monitoring and troubleshooting:
	- Use Application Insights.
	- Limited metrics to monitor and persist the history in 1 month.
	- Metrics recommended being monitored are Capacity and Requests.
	- To track the subscription, product, or API usage, the best option is to visit the Analytics page in the Monitoring section.
	- Troubleshooting can be performed from the Azure portal by executing a request to the APIM operation. The Test functionality allows you to send a request to APIM and trace the flow on the Trace tab. The Backend, Inbound, Outbound, and On error tabs let you observe the output of each of the stages and traces.
- Advanced policies:
	- Mocking API responses.
	- Caching API response
	- Throttling requests
	- Controlling flow
## Versions
- Usages:
	- Publish multiple versions of your API at the same time.
	- Use path, query string, or header to differentiate between versions.
	- Use any string value you wish to identify you version, which could be a number, a date, or a name.
	- Show you API versions grouped together on the developer portal.
	- Take an existing (non-versioned) API, and create a new version of it without breaking existing clients.
- Versioning schemes:
	- Path-based versioning:
		- Format of an API request URL when using path-based versioning is: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}`
		- Example:
			- Version 1: `https://apis/contoso.com/products/v1`
			- Version 2: `https://apis/contoso.com/products/v2`
	- Header-based versioning:
		- The version identifier needs to be included in an HTTP request header for any API requests.
	- Query string-based versioning
		- The version identifier is in the query string of the request: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}`
```ad-note
query parameters are not allowed in the `servers` property of an `OpenAPI` specification.
```
- A version can have multiple revisions. You can use revisions without using version, just like a non-versioned API. Versions are used to separate API versions with breaking changes, while revisions can be used for minor and non-breaking changes to an API.
## Backend API
- Benefits:
	- Abstracts information about the backend service, promoting reusability across APIs and improved governance.
	- Easily used by configuring a transformation policy on an existing API.
	- Takes advantage of API management functionality to maintain secrets in Azure Key Vault if named values are configured for header or query parameter authentication.
## Policy management
- The policy XML configuration is divided into `inbound`, `backend`, `outbound` and `on-error` sections. This series of specified policy statements is executed in order for a request and a response.
`````` xml
<policies>
	  <inbound>
	    <!-- statements to be applied to the request go here -->
	  </inbound>
	  <backend>
	    <!-- statements to be applied before the request is forwarded to 
	         the backend service go here -->
	  </backend>
	  <outbound>
	    <!-- statements to be applied to the response go here -->
	  </outbound>
	  <on-error>
	    <!-- statements to be applied if there is an error condition go here -->
	  </on-error>
</policies>
``````
- APIM allows you to define policies at the following scopes, from most board to most narrow:
	- Global (all APIs).
	- Workspace (all APIs in selected workspace).
	- Product (all APIs in selected product).
	- API (all operations in an API).
	- Operation (single operation in an API).
	  ![[Pasted image 20231123230813.png]]
## Things to know
- For fine-grained control for different API consumers, you can configure policy definition at more than 1 scope.
- Not all policies are supported at each scope and policy section.
- When configuring policy definitions at more than 1 scope, you control policy inheritance and the policy evaluation order in each policy section by placement of the `base` element.
- Policies applied to API requests are also affected by the request context, including the presence or absence of a subscription key used in the request, the API or product scope of the subscription key, and whether the API or product requires a subscription.
```ad-note
- You manage an instance of Azure API Management. You define policies to multiple scopes. You need to enforce a policy evaluation order.
	- Use `<base />`, it provides the ability to enforce policy evaluation order.
	- The `<when />` element is part of the choose policy and is evaluated in order of its appearance within the policy.
	- The `follow-redirects` attribute is part of the forward request policy, so it does not have any impact on the policy evaluation order.
	- The `condition` attribute is part of the retry policy, so it does not have any impact on the policy evaluation order.
- You plan to use Azure API Management for Hybrid and multicloud API management. You need to create a self-hosted gateway for production.
	- Use `2.0.1`. In production, the version must be pinned. The only way tho achieve that is by using a tag that follows the convention `{major}.{minor}.{path}`.
	- The `V3` tag will result in always running a major version with every new feature and patch.
	- The latest tag is used for evaluating the self-hosted gateway.
	- The `V3-preview` tag should be used to run the latest preview container image.
```
