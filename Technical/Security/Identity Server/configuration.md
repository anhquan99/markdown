# Configuration
## API scope
- Scope is a core feature of OAuth that allows you to express the extent or scope of access. Clients request scopes when they initiate the protocol, declaring what scope of access they want. IdentityServer then has to decide which scopes to include in the token. Just because the client has asked for something doesn’t mean they should get it! There are built-in abstractions as well as extensibility points that you can use to make this decision. Ultimately, IdentityServer issues a token to the client, which then uses the token to access APIs. APIs can check the scopes that were included in the token to make authorization decisions.
# Clients
- Minimal machine to machine client:
	- A ClientId: identifies the application to IdentityServer (IDS) so that it knows which client is trying to connect to it.
	- A Secret: like the password for the client.
	- List of scopes that the client is allowed to ask for.
```ad-note
On first startup, IdentityServer will use its automatic key management feature to create a signing key and store it in the _src/IdentityServer/keys_ directory. To avoid accidentally disclosing cryptographic secrets, the entire _keys_ directory should be excluded from source control. It will be recreated if it is not present.
```
## IdentityModel
- Nuget package `IdentityModel`.
- The token endpoint at IdentityServer implements the OAuth protocol, and you could use raw HTTP to access it. However, we have a client library called IdentityModel that encapsulates the protocol interaction in an easy to use API.
