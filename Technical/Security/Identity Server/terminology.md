# Terminology
![[Pasted image 20250118120637.png]]
## IdentityServer
- A piece of software that issues security tokens to clients.
- Features:
	- Protect resources
	- Authenticate users using local account store or via an external identity provider
	- Provide session management and single sign-on
	- Manage and authenticate clients
	- Issue identity and access tokens to clients
	- Validate tokens
## User
- A human that is using a registered client to access resources.
## Client
- A client is a piece of software that requests tokens from IdentityServer - either for authenticating a user (requesting an identity token) or for accessing a resource (requesting an access token). A client must be first registered with IdentityServer before it can request tokens.
## Resources
- Resources are things to protect, like data of users, or APIs.
- Every resource has a unique name, and clients use this name to specify to which resources they want to get access to.
- **Identity data** Identity information (aka claims) about a user, e.g. name or email address.
- **APIs** resources represent functionality a client wants to invoke - typically modeled as Web APIs, but not necessarily.
## Identity token
- An identity token represents the outcome of an authentication process.
## Access token
- An access token allows access to an API resource. Clients request access tokens and forward them to the API. Access tokens contain information about the client and the user (if present). APIs use that information to authorize access to their data.