- [[Technical/DevOps/Azure/Az 204/Exam guide/API Management|API Management]] alike.
- Handle different env.
- Handle security (authentication and authorization).
# Concepts
- Callback URL: when  a new client is connected through a WebSocket connection, you can call integration in API Gateway to store the client's callback URL. You can then use that callback URL to send messages to the client from the backend system.
# EndPoint types
## Edge-optimized (default)
- For global clients.
- Requests are routed through the CloudFront Edge location (improves latency).
- The API Gateway still lives in only 1 region.
## Regional
- For client within the same region.
- Could manually combine with CloudFront (more control over the caching strategies and the distribution).
## Private
- Can only be accessed from your VPC using an interface VPC endpoint (ENI).
- Use a resource policy to define access.
# Security
- User Authentication through
	- IAM Roles
	- Cognito
	- Custom Authorizer
- Custom Domain Name HTTPS security through integration with AWS Certificate Manager (ACM)
	- If using Edge-Optimized endpoint, then the certificate must be in `us-east-1`.
	- If using Regional endpoint, the certificate must be in the API Gateway region.
	- Must setup CNAME or A-alias record in Route 53.
# Stage
## Variables
- We create a stage variable to indicate the corresponding Lambda alias, the API Gateway will automatically invoke the right Lambda function.
# Deployment
## <mark style="background: #BBFABBA6;">Canary</mark>
# Integration types
## <mark style="background: #BBFABBA6;">Mock</mark>
- API Gateway returns a response without sending the request to the backend.
## <mark style="background: #BBFABBA6;">HTTP / AWS (Lambda and AWS Services)</mark>
- Configure both the integration request and response, setup data mapping using **mapping templates** for the request and response.
## <mark style="background: #BBFABBA6;">AWS_PROXY</mark>
- Incoming request from the client is the input to Lambda.
- No mapping template, headers, query string parameters ... are passed as arguments.
## <mark style="background: #BBFABBA6;">HTTP_PROXY</mark>
- No mapping template.
- The HTTP request is passed to the backed and HTTP response from the backend is forwarded by API Gateway.
- Possibility to add HTTP Headers if it need to be.
  # Mapping templates
  - Can be used to modify request / response.
  - Content-Type can be set to `application/json` or `application/xml`.
  # Open API spec
  - Common way of defining REST APIs, using API definition as code.
  - Import existing OpenAPI 3.0 spec to API Gateway:
	  - Method
	  - Method request
	  - Integration request
	  - Method response
	  - AWS extensions for API gateway and setup every single option.
  - Can export current API as OpenAPI spec which can be written in YAML or JSON.
  - Using OpenAPI we can generate SDK for our applications.
  # Request Validation
  - Using `x-amazon-apigateway-request-validators`.
  # Caching API responses
  - Default TTL is 300 secs (min 0s, max 3600s).
  - Caches are defined per stage.
  - Cache capacity 0.5 GB > x < 237 GB.
  - Cache is expensive, prefer used in production.
  - Has encryption option.
  ## Invalidation
  -  Flush entire cache immediately.
  - Invalidate the cache with header `Cache-Control: max-age=0` (with proper IAM).
  # Usage plan and API keys
  - Make API as business offer.
  ## <mark style="background: #BBFABBA6;">Usage plan</mark>
  - Who can access the API stages and methods.
  - How much and how fast they can access them.
  - Uses API keys to identify API clients and meter access.
  - Configure throttling limits and quota limits that are enforced on individual client.
  ## <mark style="background: #BBFABBA6;">API keys</mark>
  - Alphanumeric string values to distribute to your customers.
  - Can use with usage plans to control access.
  - Caller must supply an assigned API key in the `x-api-key` header in requests to the API.
# Throttling
- Account limit:
	- API Gateway throttles request at 10000 rps across all API.
	- Soft limit than can be increased upon request.
- Return **429 Too many Requests**.
- Can set Stage limit and method limits to improve performance or usage plan to throttle per customer.
# Erros
- 4xx means client errors
- 5xx means server errors:
	- 502 Bad Gateway Exception, usually for an incompatible output returned from a Lambda proxy integration backend and occasionally for out-of-order invocations due to heavy loads.
	- 504: integration failure - ex endpoint request timed-out exception.
# CORS
- The OPTIONS pre-flight request must contain the following headers:
	- `Access-Control-Allow-Methods`
	- `Access-Control-Allow-Headers`
	- `Access-Control-Allow-Origin`
# Security
## Cognito User Pools
- Cognito fully manages user lifecycle, token expires automatically.
-  API Gateway verifies identity automatically from AWS Cognito.
## Lambda authorizer
- Token-based authorizer (bearer token).
- A request parameter-based (headers, query string, stage var).
- Lambda returns an IAM policy for the user.
## Summary
### IAM: 
- Great for users / roles already within your AWS account, + resource policy for cross account.
- Handle authentication + authorization.
- Leverages Signature v4.
### Custom Authorizer:
- Great for 3rd party tokens.
- Very flexible in terms of what IAM policy is returned.
- Handle Authentication verification + Authorization in the Lambda function.
- Pay per Lambda invocation, results are cached.
### Cognito User Pool:
- You manage your own user pool (can be backed by Facebook, Google login etc…).
- No need to write any custom code • Must implement authorization in the backend.
# WebSocket API
1. Connect to API Gateway and generate a connectionId to the next calls.
2. The next calls are called with **Connection URL callback** with the connectionID.
# Architecture
- Create a single interface for all the microservices in your company
- Use API endpoints with various resources
- Apply a simple domain name and SSL certificates
- Can apply forwarding and transformation rules at the API Gateway level