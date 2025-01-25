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
### Identity resources
- An identity resource is a named group of claims about a user that can be requested using the _scope_ parameter.
- The OpenID Connect specification suggest a couple of standard scope name to claim type mappings. One of them is mandatory, the `openid` scope, which tells provider to return the `sub` (subject id) claim in the identity token.
```C#
// a custom identity resource represents the display namem email address and website claim.
new IdentityResource( 
	name: "profile", 
	userClaims: new[] { "name", "email", "website" },
	displayName: "Your profile data")
```
- Once the resource is defined, you can give access to it to a client via `AllowedScopes` option.
```C#
var client = new Client
{
	ClientID = "client",
	AllowedScopes = { "openid", "profile" }
};
```
- The client can then request the resource using the scope parameter `https://demo.duendesoftware.com/connect/authorize?client_id=client&scope=openid profile`.
### API scopes
- It is the scope of access that the client request. The scope parameter is a list of space delimited values - you need to provide the structure and semantics of it.
- In more complex systems, often the notion of a _resource_ is introduced. This might be e.g. a physical or logical API. In turn each API can potentially have scopes as well. Some scopes might be exclusive to that resource, and some scopes might be shared.
- The consumer of the access token can use that data to make sure that the client is actually allowed to invoke the corresponding functionality.
```ad-warning
Be aware, that scopes are purely for authorizing clients, not users. In other words, the _write_ scope allows the client to invoke the functionality associated with the scope and is unrelated to the user’s permission to do so. This additional user centric authorization is application logic and not covered by OAuth, yet still possibly important to implement in your API
```
- More identity information about the user can be added to the access token. The additional claims added are based on the scope requested.
### API resources
- The `ApiResource` class allows for some additional organization as well as grouping and isolation of scopes as well as providing some common settings.
- Features:
	- Support for the JWT `aud` claim. The value(s) of the audience claim will be the name of the API resource(s)
	- Support for adding common user claims across all contained scopes
	- support for introspection by assigning an API secret to the resource
	- support for configuring the access token signing algorithm for the resource
- You can add user claims to an API resource.
```C#
var customerResource = new ApiResource("customer", "Customer API")
    {
        Scopes = { "customer.read", "customer.contact", "manage", "enumerate" },
        
        // additional claims to put into access token
        UserClaims =
        {
            "department_id",
            "sales_region"
        }
    }
```
## Identity token
- An identity token represents the outcome of an authentication process.
## Access token
- An access token allows access to an API resource. Clients request access tokens and forward them to the API. Access tokens contain information about the client and the user (if present). APIs use that information to authorize access to their data.