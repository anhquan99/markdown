- Azure key vault is used for storing and accessing secrets.
- Problem solves:
	- Secrets management.
	- Key management.
	- Certificate management.
- Tiers:
	- Standard: encrypts with a software key.
	- Premium: includes hardware security module (HSM) protected keys.
- Benefits:
	- Centralized application secrets
	- Securely store secrets and keys
	- Monitor access and use
	- Simplified administration of application secrets
- Authentication with Azure key vault:
	- Manage identities for Azure resources: assign an identity to the resources that has access to key vault. This is recommended as a best practice.
	- Service principal and certificate: Microsoft is not recommend is approach because the application owner or developer must rotate the certificate.
	- Service principal and secret: another not recommended approach because it's hard to automatically rotate the bootstrap secret that's used to authenticate to key vault.
- Encryption of data in transit: Azure key vault enforces TLS protocol to protect data when it's traveling between Azure key vault and clients.
- Best practices:
	- Use separate key vaults.
	- Control access to your vault.
	- Backup.
	- Logging.
	- Recovery options: turn on soft-delete and purge protection if you want to guard against force deletion.
# Managed identities
- Provides an automatically managed identity in Azure AD for application to use when connecting to resources that support Azure AD authentication.
- Types:
	- System assigned managed identity: tied to the Azure resource instance.
	- User assigned managed identity: stand alone Azure resource.
- How a system-assigned managed identity works with an Azure virtual machine:
	1. Azure Resource Manager receives a request to enable the system-assigned managed identity on a virtual machine.
	2. Azure Resource Manager creates a service principal in Azure Active Directory for the identity of the virtual machine. The service principal is created in the Azure Active Directory tenant that's trusted by the subscription.
	3. Azure Resource Manager configures the identity on the virtual machine by updating the Azure Instance Metadata Service identity endpoint with the service principal client ID and certificate.
	4. After the virtual machine has an identity and use the service principal information to grant the virtual machine access to Azure resources. To call Azure Resource Manager, use role-based access control in Azure Active Directory to assign the appropriate role to the virtual machine service principal. To call Key Vault, grant your code access to the specific secret or key in Key Vault.
	5. Your code that's running on the virtual machine can request a token from the Azure Instance Metadata service endpoint, accessible only from within the virtual machine: `http://169.254.169.254/metadata/identity/oauth2/token`
	6. A call is made to Azure Active Directory to request an access token (as specified in step 5) by using the client ID and certificate configured in step 3. Azure Active Directory returns a JSON Web Token (JWT) access token.
	7. Your code sends the access token on a call to a service that supports Azure Active Directory authentication.
- How a user-assigned managed identity works with an Azure virtual machine
	1. Azure Resource Manager receives a request to create a user-assigned managed identity.
	2. Azure Resource Manager creates a service principal in Azure Active Directory for the user-assigned managed identity. The service principal is created in the Azure Active Directory tenant that's trusted by the subscription.
	3. Azure Resource Manager receives a request to configure the user-assigned managed identity on a virtual machine and updates the Azure Instance Metadata Service identity endpoint with the user-assigned managed identity service principal client ID and certificate.
	4. After the user-assigned managed identity is created, use the service principal information to grant the identity access to Azure resources. To call Azure Resource Manager, use role-based access control in Azure Active Directory to assign the appropriate role to the service principal of the user-assigned identity. To call Key Vault, grant your code access to the specific secret or key in Key Vault.
	5. Your code that's running on the virtual machine can request a token from the Azure Instance Metadata Service identity endpoint, accessible only from within the virtual machine: `http://169.254.169.254/metadata/identity/oauth2/token`
	6. A call is made to Azure Active Directory to request an access token (as specified in step 5) by using the client ID and certificate configured in step 3. Azure Active Directory returns a JSON Web Token (JWT) access token.
	7. Your code sends the access token on a call to a service that supports Azure Active Directory authentication
# Azure app configuration
- Benefits:
	- A fully managed service that can be set up in minutes
	- Flexible key representations and mappings
	- Tagging with labels
	- Point-in-time replay of settings
	- Dedicated UI for feature flag management
	- Comparison of two sets of configurations on custom-defined dimensions
	- Enhanced security through Azure-managed identities
	- Encryption of sensitive information at rest and in transit
	- Native integration with popular frameworks
- It's easier to implement in these scenarios:
	- Centralize management and distribution of hierarchical configuration data for different environments and geographies
	- Dynamically change application settings without the need to redeploy or restart an application
	- Control feature availability in real-time
- Keys:
	- Case sensitive and unicode-based strings.
	- Design key namespaces:
			- Flat.
			- Hierarchical:
		- Easier to read. Instead of one long sequence of characters, delimiters in a hierarchical key name function as spaces in a sentence.
		- Easier to manage. A key name hierarchy represents logical groups of configuration data.
		- Easier to use. It's simpler to write a query that pattern-matches keys in a hierarchical structure and retrieves only a portion of configuration data.
- Manage application features:
	- Feature flag
	- Feature manager: manage feature flag.
	- Filter: is a rule for evaluating state of feature flag, example: device, OS, country ...