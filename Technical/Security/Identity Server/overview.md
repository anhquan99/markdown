# Overview
 ![[Pasted image 20250118101332.png]]
## Problems
- Usually every layer application layer has to protect resource and implement authentication and/or authorization - often against the same user store.
- Duplicate security functions.
## Solution
- Outsourcing security functions to a security token service.
![[Pasted image 20250118102558.png]]
## Security concerns
### Authentication
- Authentication is needed when an application needs to know the identity of the current user, these applications manage data on behalf of that user and need to make sure that this user can only access the allowed data.
- Protocols:
	- SAML2p
	- WS-Federation
	- OpenID Connect
### API Access
- Applications have 2 fundamental ways to communicate with APIs - using the application identity, or delegating the user's identity. Sometimes both methods need to be combined.
- OAuth2 is a protocol that allows applications to request access tokens from a security token service and use them to communicate with APIs. This delegation reduces complexity in both the client applications and the APIs since authentication and authorization can be centralized.
## OpenID Connect and OAuth 2.0
- OpenID Connect and OAuth 2.0 are very similar - in fact OpenID Connect is an extension on top of OAuth 2.0. The 2 fundamental security concerns are combined into a single protocol - often with a single round trip to the security token service.
- IdentityServer4 is an implementation of these 2 protocols.
- IdentityServer is middleware that adds the spec compliant OpenID Connect and OAuth 2.0 endpoints to an arbitrary ASP.NET Core application.
![[Pasted image 20250118114641.png]]