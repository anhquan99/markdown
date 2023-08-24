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