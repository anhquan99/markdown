## Azure Key Vault
- Authorization:
	- RBAC: controls access to the management plan, which is used for creating and managing key vaults and their vaults and their attributes and access policies, and accessing the data stored within them.
	- Key vault access policy: access policies control access to the data plane for managing secrets, keys, and certificates, but not the management of the key vault itself.
- Authorization:
	- Implementing managed identities: the secret and secret rotation is automatically handled by Azure. You don't even have access to the credentials. This is the recommended way to authenticate your application with Key Vault and can be used to authenticate with any resources that support AAD authentication, even your own applications.
		- User-assigned managed identity
			- Is a standalone resource tied to an AAD identity that's trusted by the subscription on which it's created. Once you've created a user-assigned managed identity, you can assign it to 1 or more applications. With this type of managed identity being standalone and assignable to multiple resources, its life cycle is managed separately to any of those resources.
		- System-assigned managed identity
			- A system-assigned managed identity is enabled within a resource and shares the life cycle of that resource.
## Azure App Configuration
- You can centrally manage your configuration settings with Azure App Configuration, so you don't have to save all your settings in each individual component.
- With App Configuration, you can create key-value pairs, and each setting can also be tagged with a label, so you can have the same key name with multiple value and labels.
- All settings within App Configuration are encrypted at rest and in transit, but this doesn't make App Configuration replace Key Vault because of the hardware-level encryption, access policy mode and features such as certificate rotation, .... You can create an App Configuration setting that pulls a value from a Key Vault secret, so your application can reference the App Configuration key and the value will come from Key Vault.
- Compare with the App Setting in Web App. When changing any value in App Setting, the application needs to reset to apply new setting but with App Configuration the application is not needed to reset.
- Feature flags: it is a Boolean variable which has some code that executes based on the value of the flag.