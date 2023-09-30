# Implementing IaaS solutions
1. 2 - C wrong
Although having VMs within an availability zone will bring VMs closer together than if they were in different availability zones or regions, proximity placement groups ensure that they are physically located close to each other for when you have low latency requirements.

2. 1 - B wrong
The default deployment mode is incremental, which will only make changes to the resources defined in the template if they need to be changed but won’t delete any resources. The complete deployment mode will delete any resources within the deployment scope (the resource group, in this case) that are present within that scope but not defined within the template.

3. 3 - C
Commands with az acr refer to Azure Container Registry and are not executed on the local machine. The docker run command requires a container to have already been built, which happens via the docker build command.

4. 3 - A wrong
ACI container groups allow you to host multiple containers on the same host machine, sharing the same life cycle, resources, network, and storage volumes. As the ACI infrastructure already exists, this option is more suitable than a Kubernetes Pod, which would require new infrastructure and therefore an additional cost.
Azure Container Registry is used for storing but not running container images, and a Log Analytics workspace won’t help with obtaining the logging information or writing it to long-term storage.

5. 4 - C wrong
To have a name specified at deployment time without hardcoding the value in the template, you should create a parameters section in the template with a parameter for the name. You should also change the name value of the resource to get the value from that parameter. During deployment, the staName parameter (in this example) can be specified to create a new resource with that name, providing the name is available.
# Creating Azure App Service web apps
1. 4 - B && C wrong
Filesystem storage is intended for short-term logging and disables itself after 12 hours, so it won’t be useful in this scenario, leaving Blob storage as the storage solution of choice. Windows apps offer application logging to both Blob and filesystem storage, whereas Linux only offers application logging to filesystem storage, making a Windows App Service plan a requirement to meet the needs of this scenario.

2. 3 - C
The error indicates that cross-origin resource sharing (CORS) is blocking requests from `https://az204.com`. The command should be run from `myapi` rather than `myapp` because CORS is configured on the destination to specify where requests are accepted from.

3. 4 - B wrong
Hybrid Connections can be used to provide Azure App Service web apps with access to resources in any network, including on-premises networks. This requires a relay agent within the network, which will relay the request from the web app to the on-premises TCP endpoint. The endpoint will then communicate with the web app over port 443 in an outbound connection from that TCP endpoint

4. 1 - A
When multiple scale-out rules are triggered, autoscale evaluates the new capacity of each rule triggered and takes the scale action that will result in the greatest capacity of those triggered rules. In this scenario, that would be 7. Autoscale doesn’t combine the instance counts of multiple rules – it only selects the single action that provides the greatest capacity.
# Implementing Azure Funtions
1. 3 - C
On the Consumption plan, functions that have been idle for a period will be scaled down to zero instances. After this happens, the first request may experience some latency because a cold startup is required to scale back up from zero.

2. 3 - C

3. 1 - A
With C# script, JavaScript, PowerShell, Python, and TypeScript functions, the function.json file needs to be updated to configure triggers and bindings.
In C# and Java, you decorate methods and parameters in code.
# Cosmos DB storage
1. 3 - D wrong
The Cosmos DB change feed cannot be queried because it is a FIFO queue. Each modification is registered as a message in the queue and can be pulled by consumers.

2. 3 - B wrong
There are physical and logical partitions that exist for Cosmos DB. Its structure is controlled internally by Cosmos DB. The number of physical partitions depends on the throughput and storage capacity of the documents stored in the partition.

3. 5 - E
The client-side encryption (Always Encrypt) meets the requirements because it protects data at rest and in transit and is decrypted only on the client side by using customer-managed keys from Azure Key Vault. When you apply encryption settings, you need to provide the path of a card number field. Full documents cannot be encrypted.

4. 4 - B wrong
If the update needs to be applied for both of the regional instances at the same time to minimize possible data loss, it means a strong consistency level for operation. The operation consistency must be the same or more relaxed than the default consistency. So, increasing the default level to strong is only one solution.

5. 1 - D
The solution using of a single DB and consistency for a single DB makes no sense. Meanwhile, the indexing process affects the performance of inserting. One of the possible solutions is to set the index mode to none from the code before submitting the bulk. Then, return to the normal state.
# Azure Blob storage
1. 3 - C
- The static website hosted on the Azure storage account provides the following:
- Availability in two regions if the GRS option is chosen.
- Versioning for all files in the blob including pages of the website.
- The option to register custom domains.
- Minimal cost in comparison with VMs and App Services.

2. 3 - D wrong
The premium storage account does not have access tiers. The Archive tier will be expensive because of writing transactions to append the log files. The Hot tier will be a more economically sound choice.

3. 4 - A wrong
The SAS is generated by using one of the admin keys. Regenerating the admin keys will break all SAS generated with the previous keys. This is an acceptable solution because the application is still in the testing phase and other devices are available for refreshing its SASs. Only SASs generated with a stored access policy can be safely revoked.

4. 4 - A wrong
The code is taken from one of the demo projects for setting metadata. The code works without errors if the container exists. Before completing any operation with the container, the code should ensure that container exists and call the CreateIfNotExists function.

  

# Authentication and authorization
1. 2 - C wrong
For applications to integrate with Azure Active Directory (AAD), an app registration must be created with at least the User.Read delegated permission assigned.

2. 1 - A
Because this is an app that runs on user devices, it is therefore not trusted with application secrets and is only able to request access to resources on behalf of a logged-in user – this is a public client and not a confidential client.

3. 1, 5, 3 - A, C, D wrong
Use the az storage container policy create command to create a new stored access policy. Then, run the az storage container generate-sas command to generate a new SAS token that uses the new policy. Finally, run the az webapp config appsettings set command to update the relevant application setting of the App Service with the new SAS token.
# Secure cloud solutions
1. 1 - A && B
The az webapp identity assign command will assign a system-assigned managed identity to the web app in question, which will share the same life cycle as the web app. The az identity create command will create a user-assigned managed identity, which is a standalone resource and won’t share the same life cycle as the web app and therefore doesn’t meet the requirements.
The az keyvault set-policy command will set an access policy on the relevant Key Vault, so you can provide the system-assigned managed identity with the permissions required to access the data plane of the vault. The az policy assignment create command relates to Azure Policy, not a Key Vault access policy, so it won’t help in this scenario.

2. 4 - D
An App Configuration resource allows you to centrally manage both the application configuration settings and feature flags. App Configuration isn’t a replacement for Key Vault for storing secrets, but you can also create a new App Configuration key that pulls the value from Key Vault (although it’s not a requirement in this scenario).

3. 2 - B
You can import key-value pairs into App Configuration using the az appconfig kv import command. You can also export them from App Configuration into a JSON file using the az appconfig kv export command.
# Caching and CDN
1. 1 - A
The TCP protocol is used for communication between the cache instance and the client.

2. 2 - E wrong
From the list of tools, only Azure CLI can not be used for observing values, but it can be used for managing the cache instance and importing and exporting data.

3. 1 - C wrong
Azure Cache for Redis suits server-side caching for reusing content better than other services from the list. It also provides flexibility with setting custom TTL for the response. Moreover, Azure Cache for Redis will provide better performance than a storage account because of TCP communication.

4. 6 - D wrong
The update file is static content and should be cached for clients with the appropriate service. Server-side caching (Azure Cache for Redis) does not help the client. The update of 50 MB does not suit general web hosting (App Service) well because the interruption to the connectivity will lead to downloads restarting. If the download speed is a requirement, the file should be located as close as possible to the clients. CDN servers (point-of-presence) will be the best choice. Downloading from an Azure storage account from another region will be slow and expensive. Outgoing traffic from the data center adds an extra charge to the subscription.

5. 4 - D
The CDN endpoint still cached the old version of the JS file. You need to wait until the cached copy expires or purge the content.
# Monitoring and logging
1. 1 - B wrong
Application Insights dependency tracking can collect SQL queries and their performance. You can get access to the collected information from the Application Map and Performance sections of the Application Insights page. Azure SQL Insights can only track query performance for all application requests. Profiler only helps for functions running from code.

2. 5 - E
The code snippet represents working code from the controller that handles the client list loading from the DB (Entity Framework). The code tracks custom metrics with the elapsed time of the dependency collected by the Stopwatch class.

3. 1 - D wrong
The code snippet represents working code from the controller that handles the client list loading from the DB (Entity Framework). The code tracks custom metrics with the elapsed time of the dependency collected by the Stopwatch class.

4. 5 - D wrong
Debugging snapshots will help you to investigate the crash in a less invasive way. You can observe the snapshot from the Application Insights page or download and open it in Visual Studio. Live debugging and setting breakpoints can help with investigating the issue but freezes the application when the breakpoint is reached which interrupts request processing.
# API management

1. 2 - B

2. 4 - D

3. 1, 2, 3, 5 - D wrong
Debugging snapshots will help you to investigate the crash in a less invasive way. You can observe the snapshot from the Application Insights page or download and open it in Visual Studio. Live debugging and setting breakpoints can help with investigating the issue but freezes the application when the breakpoint is reached which interrupts request processing.
# Event based
1. 6 - C wrong
To minimize cost and admin efforts, you should leverage out-of-box functionality such as event capturing. Event capturing is only available with the Standard and Premium tiers. The additional service Azure Data Lake should be deployed for storing captured events. The charges for Azure Data Lake are less than the consumption charges from Event Grid per each of the events and storing their content in Cosmos DB. Data Lake is a consumption-based service deployed as an extension for an Azure storage account. Capturing event content is impossible with Azure Monitor, Application Insights, or an Azure Log Analytics workspace.

2. 4 - D
There is no out-of-the-box functionality that meets the requirements. The IoT Hub should be upgraded to the Standard SKU to support dynamic settings management (or twin settings). The response time will be decreased if the IoT device starts cooling as soon as the temperature hits the threshold.
The IoT edge will suit the requirements but will also require you to upgrade the IoT Hub and operate with Docker containers that increase the maintenance effort. The same is true for rebuilding containers.
B is a workable option but requires you to build expensive extra services.

3. 3 - C
az eventhubs event hub authorization-rule create should be used with Send permission at most to follow the principle of least privilege.
# Message based
1. 2 - A wrong
The correct name of the metric for Storage Queue is approximate message count because the exact message count is not available.

2. 2 - D wrong
Azure Service Bus Queue guaranteed the sequence of messages. The SDK automatically implements a retry pattern when the application experiences transient connection errors.

3. 2. - C wrong
ServiceBusProcessor provides callback architecture that allows the processing of received messages in real time by registering on ProcessMessagesAsync events.