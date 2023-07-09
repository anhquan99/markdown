- Stands for Internet Information Service. IIS is a web server developed by Microsoft and used to host the Web application. It has its own ASP.NET process engine to handle the request.
- When the request comes from client to server, IIS takes that request and process it and send the response to back clients.
- Web server is used to hosting the web application on a centralized location and can be accessed from many locations. It is responsible for handling all the requests that are coming from clients, processes the requests and provides responses.
# Web Garden
- By default, each application pool runs with a single worker process (W3Wp.exe). When an application pool runs with multiple worker process, it's called web garden.
- Pros: share the requests which comes for that application pool and if one worker process fails to process a request then another process can continue to process that request.
- Cons: when you use session mode to `In Proc` with your application, it will not work properly as expected since session will be handled by different worker process. For avoiding this issue, use mode `Out Proc` having `Session State Server` or `SQL-Server Session State`.
# Web Farm
- When a web application is hosted on multiple web servers and access based on the load (load balancer) on servers, it's called web farm.
- Pros:
	- When a web server down, request will be redirected to another web server available.
	- Provides high performance response for the client requests.
- Cons: same as web garden.
# Application Pool in IIS
- An IIS application pool is a set of URLs that is routed to one or more worker processes.
- Application pools, responsible for to isolate one or more applications into their own process.
- Key points:
	- Provides isolation between different web applications.
	- Every web application has individual worker process.
	- Improve manageability of web application.
	- Provides better performance.
- Configs:
	- Recycling.
	- Performance.
	- Health.
	- Identity.
# IIS Architecture
- 2 main layers:
	- Kernel mode: contains HTTP.SYS
	- User mode: contains WAS and W3 service.
![[Pasted image 20230709113636.png]]
- Request process flow:
	1. HTTP request from client got to HTTP.sys then HTTP.sys passing the request to a application pool.
	2. HTTP.sys contacts to WAS and WAS requests configuration information from the xml file.
	3. The configuration information is sent to WWW service receives.
	4. WWW service uses the configuration information to configure HTTP.sys.
	5. Configured HTTP.sys contacts to WAS and starts a worker process for the application pool to which the request was made.
	6. The worker process processes the request and return a response to HTTP.sys. The request is passed through an ordered series of module in the processing pipeline.
- Role of HTTP.sys in IIS:
	- Routing HTTP request to the correct request queue.
	- Caching of responses in kernel mode.
	- Performing all text-based logging for the WWW service.
	- Implementing quality of service functionality, which includes connection limits, connection timeouts, queue length limits, and bandwidth throttling.
- ISAPI Filter:
	- Change request data (URLs or headers) sent by the client.
	- Control which physical file gets mapped to the URL.
	- Control the username and password used with anonymous or basic authentication.
	- Modify or analyze a request after authentication is complete.
	- Modify a response going back to the client.
	- Run processing when a request is complete.
	- Run processing when a connection with the client is closed.
	- Perform special logging or traffic analysis.
	- Handle encryption and compression.