- Microsoft's identity platform is built on top of the open standard:
	- OpenID Connect (OIDC): for authentication.
	- OAuth 2.0: for authorization.
## Service principles
- When you want to delegate functions to Azure Active Directory (AAD), you need to register with an AAD tenant, which create an identity configuration for your application that allows integration with AAD.
- **Single tenant**: only accessible within the AAD tenant in which you're creating the registration, also known as the home tenant.
- **Multi tenant**: accessible from other AAD tenants.
- When you register an application with AAD, an application object is created within the home tenant. This object contains metadata about your application, how tokens get issued in order to access the app, details of any resources the app might need access to, and which actions the app can perform. Each application will have only 1 app registration, even if it's been configured to be accessible from multiple tenants. This information is used as a template for creating 1 or more service principles.
- App registration is the global application object, and the service principles (enterprise application) is the per-tenant representation of that object.
## Permission and consent types
- Any web-hosted resources that integrate with the Microsoft identity platform can also define a set of permissions, allowing for third-party apps to request only the permissions they need to perform their functions. Users and admins can see what type of data the app can access.
- In OAuth 2.0, permission sets are known as scopes.
## Permission types
- Delegated permissions: used when an application needs to act on behalf of the signed-in user when it makes calls to the target resource.
- Application permissions: used when an application needs to call a target resource without a signed-in user present.
## Consent types
- Incremental and dynamic user consent:
	- You can request only minimal permissions upfront, if the application requests additional permissions as, and they're needed. User will be prompted to provide consent only applicable to those new permissions being requested.
	- This consent type can only apply for delegated permissions. For application permissions, the application needs to know all the permissions when request resource.
- Static user consent: 
	- Permissions are already specified in the application configuration.
	- Like incremental and dynamic user consent, the application must know all the permissions to request resources.
- Admin consent: the admin is asked for the permission rather than user. These permissions need to be static permissions registered within the app registration portal.
## Microsoft Authentication Library (MSAL)
- MSAL allows you to get tokens from the Microsoft identity platform for authentication and accessing secure web APIs. It handles:
	- Use OAuth and OIDC library directly, while you just use MSAL APIs.
	- Handles the protocol level details so that you don't have to.
	- Obtains tokens on behalf of users or applications as applicable.
	- Caches and refreshes tokens when required so that you don't have to handle token expiration yourself.
	- Provides support for any Microsoft identity.
	- Helps with troubleshooting your app by exposing actionable exceptions, logging, and telemetry.
- Authentication flows:
	- Authorization code: obtains tokens and accesses web APIs on the user's behalf.
	- Client credentials: access web APIs using the application's identity with no user interaction.
	- Device code: signs users in to a device without a browser, from another device that has a browser, and accesses web APIs on their behalf.
	- Implicit grant: used by browser based applications to sign in and access web APIs on behalf of the user.
	- Integrated Windows authentication (IWA): acquires a token silently when accessed from AAD joined device without user interaction.
	- On-behalf-of (OBO): accesses a downstream web API from an upstream web API on a user's behalf, sending their identity and delegated permissions.
	- Username/ password: signs the user in by directly handling their password. This is not recommended.
## Microsoft Graph
- A REST API that interact with the Microsoft 365 data through Microsoft graph.
- Components:
	- Microsoft Graph API: single endpoint for accessing Microsoft graph to interact with people-centric data insights.
	- Microsoft Graph connector: used to bring data from external sources into Microsoft graph application and services to enhance experiences.
	- Microsoft Graph Data Connect: used to access data on Microsoft graph at scale, with granular control over data and consent for admins. Data connect can access data on a recurring schedule and operates on a cache of the data in your Azure subscription rather than the data master.
## Shared access signatures (SAS)
- A SAS is a signed URI that provides defined access rights to specific resources within a storage account for a specified period.
- To use SAS for accessing Azure Storage resources, you will need:
	- The URI of the resource being accessed.
	- The SAS token that you would have created and configured.
- Structure:
	- `sp=rd`: `sp` stand for signed permission, whereas `r` is read and `d` is delete.
	- `st`: start time of token's validity.
	- `se`: end time of token's validity.
	- `spr`: signed protocol.
	- `sv`: sign resource.
	- `sig`: cryptographic signature.
- Types:
	- **User delegation SAS**: secured with AAD credentials and provides access to containers and blobs. Because a user delegation SAS is secured using specific AAD credentials, this is recommended type of SAS to use when possible.
	- **Service SAS**: secured with the storage account key and provides access to a resource (Blob storage, table storage, Azure file).
	- **Account SAS**: secured with the storage account key and provides access at the storage account level. This provides access to service level operations such as getting and setting service properties, which can't do with a service SAS. It can provide access to more than 1 service within a storage account at the same time.