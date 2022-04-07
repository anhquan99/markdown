# Single sign on
- # Process of Authentication
	- Identity Provider act as an authority, provides authentication and authrorization 
	- Client and user trusts the Identity Provider, every request to Identity Provider must use HTTPS and save the information about user as Claims and Resources.
	- Identity Provider give token to user when access
		- **Identity Token:** include to generate claims principal before it get destroyed 
		- **Access Token:** Sent with request when user access resources. It will be save for later access
		- 
# Standard
- OAuth2 - Open Authentication 2 (delegated authorization) : include only Access Token
- OpenIDConnect - OIDC (federated authentication): include Access Token and Identity Token
# Process of authorization code flow
- When client request, Identity Provider need to know which client is requesting so in the request must have the `ClientID` and `ClientSecret`  (optional) or certificate. Then Identity Provider need to know which resources or claims client need all this information will be save in scope. 
# PKCE
- PKCE is an addon of Authorization workflow
- When Client redirect to Identity Provider, client will generate a secret and Identity Provider will check if that secret was sent from client. If Identity Provider detect the secret was not sent from client it will reject and not return token
# Other flows
- Implicit and Hybrid
- Client credentials
# How does the client/api verify token?
1. Identity provider creates hash of token's content
2. Hash is encryoted using Pirvate key
3. Encrypted value is included in Token that return to Client/API (Signature)
4. Client/API uses public key decrypt
5. Client/API hash readable content in Token
6. Compare (5) with (4)
# Refresh token
- Separate token with Access token
- Used to get new Access token
- User don't need  to re-authenticate
- Longer expiration time than access token
# Reference token
- Alternative for Refresh token
- An access token that does not include claim
- Reference token contain Unique ID
- When API recive reference token it need access introspection endpoint in Identity Provider
#  Check claims on view
``````c#
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
var checkClaims = AuthorizationService.AuthorizeAsync(User, claimName);

// access the claim result
checkClaims.Result.Succeeded
``````
# To configure a complex requirements use `IAuthorizationHandler`
