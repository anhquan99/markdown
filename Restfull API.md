c# Definition
- RESTful web services are services that follow REST architecture.
- REST stand for Representational State Transfer and uses HTTP protocol for implementation
- These services are lightweight, provide maintainability, scalability, support communication among multiple applications that are developed using different programming languages.
- Provide means of accessing resources present at server required for the client via the web browser by means of request header, request body, response body, status code, ...
# REST Resource
- Every content in the REST in REST architecture is considered a resource.  They can either be represented as text file, HTML page, images or any other dynamic data.
- REST server provides access to these resources whereas the REST client consumes (access and modified) these resources. Every resource is identified globally by means of a **URI**.
# URI (Uniform Resource Identifier)
- **Format:** `<protocal>://<service-name>/<ResourceType>/<ReourceID>`
- **Type:**
	- URN: Uniform Resource Name, it doesn't always specify where to locate the resource on the internet.
		- These follow the `urn` scheme and usually prefixed with `urn:`. Examples include
		    -  `urn:isbn:1234567890` is used for identification of book based on the ISBN number in a library application.
		    -  `urn:mpeg:mpeg7:schema:2001` is the default namespace rules for metadata of MPEG-7 video.
	- URL: Uniform Resource Locator has the information resource location
		-  `http://abc.com/samplePage.html`
		-  `ftp://sampleServer.com/sampleFile.zip`
		-  `file:///home/interviewbit/sampleFile.txt`
# RESTful web services
- Based on the Cient-Server model
- Uses HTTP protocol for request and response.
- Resources are accessible to the service by means of URIs
- Stateless.
	- The session on the server is identified by the session identifier sent by the client.
- Services also uses the concept of caching to minimize the server calls of repeated requests.
# Status code
- These are the standard codes that refer to the predefined status of the task at the server. Following are the status codes formats available:
	-   1xx - represents informational responses
	-   2xx - represents successful responses
	-   3xx - represents redirects
	-   4xx - represents client errors
	-   5xx - represents server errors
	
	Most commonly used status codes are:
	
	-   200 - success/OK
	-   201 - CREATED - used in POST or PUT methods.
	-   304 - NOT MODIFIED - used in conditional GET requests to reduce the bandwidth use of the network. Here, the body of the response sent should be empty.
	-   400 - BAD REQUEST - This can be due to validation errors or missing input data.
	-   401- UNAUTHORIZED - This is returned when there is no valid authentication credentials sent along with the request.
	-   403 - FORBIDDEN - sent when the user does not have access (or is forbidden) to the resource.
	-   404 - NOT FOUND - Resource method is not available.
	-   500 - INTERNAL SERVER ERROR - server threw some exceptions while running the method.
	-   502 - BAD GATEWAY - Server was not able to get the response from another upstream server.

# HTTP Methods
- HTTP Methods are also known as HTTP Verbs. They form a major portion of uniform interface restriction followed by the REST that specifies what action has to be followed to get the requested resource. Below are some examples of HTTP Methods:
	-   GET: This is used for fetching details from the server and is basically a read-only operation.
	-   POST: This method is used for the creation of new resources on the server.
	-   PUT: This method is used to update the old/existing resource on the server or to replace the resource.
	-   DELETE: This method is used to delete the resource on the server.
	-   PATCH: This is used for modifying the resource on the server.
	-   OPTIONS: This fetches the list of supported options of resources present on the server.
- The POST, GET, PUT, DELETE corresponds to the create, read, update, delete operations which are most commonly called **CRUD Operations**.
# Disadvantages
- Stateless, it is not possible to maintain sessions. Session simulation responsibility lies on the client-side to pass the session id
- REST does not apply security restriction. It inherits security protocol through protocol implementing. With that we must integrate security measures like SSL/TLS.
# Core components of HTTP Request
-   Method/Verb − This part tells what methods the request operation represents. Methods like GET, PUT, POST, DELETE, etc are some examples.
-   URI − This part is used for uniquely identifying the resources on the server.
-   HTTP Version − This part indicates what version of HTTP protocol you are using. An example can be HTTP v1.1.
-   Request Header − This part has the details of the request metadata such as client type, the content format supported, message format, cache settings, etc.
-   Request Body − This part represents the actual message content to be sent to the server.
# Core components of HTTP Response
-   Response Status Code − This represents the server response status code for the requested resource. Example- 400 represents a client-side error, 200 represents a successful response.
-   HTTP Version − Indicates the HTTP protocol version.
-   Response Header − This part has the metadata of the response message. Data can describe what is the content length, content type, response date, what is server type, etc.
-   Response Body − This part contains what is the actual resource/message returned from the server.
# Addressing in terms of RESTful Web Services
- Addressing is the process of locating a single/multiple resources that are present on the server
- Format `<protocol>://<application-name>/<type-of-resource>/<id-of-resource>`
# Idempotent methods
- The meaning of idempotent is that even after calling a single request multiple times, the outcome of the request should be the same
# POST VS PUT
POST | PUT
------------ | ------------
Used for update operations | Used for create operations
Idempotent | Not idempotent
PUT methods are used to request the server to store the enclosed entity in request. In case, the request does not exist, then new resource has to be created. If the resource exists, then the resource should get updated | POST method is used to request the server to store the enclosed entity in the request as a new resource.  
# Why is REST services easily scalable?
- No storing of any data across the requests on the server.
# Payload
- Pass data in request body. Payload can be sent only in POST methods.
- There is no limit restriction on the size of the payload.
# Safe method vs idempotent
- **Safe method:** are methods does not change any resources internally. These methods can be cached and retrieved without any effects on the resource
- **Idempotent:** methods are those methods that do not change the responses to the resources externally. They can be called multiple times without any change in the responses.