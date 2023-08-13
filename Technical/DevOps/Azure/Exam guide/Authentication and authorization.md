## Service principles
- When you want to delegate functions to Azure Active Directory (AAD), you need to register with an AAD tenant, which create an identity configuration for your application that allows integration with AAD.
- **Single tenant**: only accessible within the AAD tenant in which you're creating the registration, also known as the home tenant.
- **Multi tenant**: accessible from other AAD tenants.
- When you register an application with AAD, an application object is created within the home tenant. This object contains metadata about your application, how tokens get issued in order to access the app, details of any resources the app might need access to, and which actions the app can perform. Each application will have only 1 app registration, even if it's been configured to be accessible from multiple tenants. This information is used as a template for creating 1 or more service principles.
- App registration is the global application object, and the service principles (enterprise application) is the per-tenant representation of that object.
## Permission and consent types
- 