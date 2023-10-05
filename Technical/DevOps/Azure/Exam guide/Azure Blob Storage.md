- Blob stand for Binary large object.
## Availability and durability
- Azure Blob storage provides 99.99999999999% durability.
- Availability:
	- Local-Redundant Storage (LRS).
	- Zone-Redundant Storage (ZRS): always keeps a copy of your data in different buildings, and each building will have a separate source of power and internet.
	- Geo-Redundant Storage (GRS): offer redundancy in a second region where another copies of your data can be stored.
		- Read-Access Geo-Redundant Storage (RAGRS): protect your application from data loss. If a data center having an outage, the access will be redirected to the second geo-redundant copy.
## Performance levels:
- Standard (v2).
- Premium: has many limitations and expensive.
## Storage access tiers:
- Hot: highest cost for storage, the lowest cost for transactions.
- Cold: in the middle. 
- Archive: lowest cost for storage, the highest cost for transactions.
## Blob types:
- Block blobs: each block has its own ID. Max size of a block blob is limited to 4.75 TB, it is the default blob type, and only block blob type can change access tier. 
- Page blobs: improving random access and ideal for storing virtual hard disk. Each blob is a combination of 512 byte pages optimized for random read and write operations. When writing to a page blob, it can overwrite just one page and commit writes immediately, so it suits making frequent updates just 1 page and commit writes immediately, so it suits making frequent updates to virtual hard disks.  The maximum size for a page is 8 TB.
- Append blobs: optimized for append operations. The type comprises blocks, and when it modifies an append blob, blocks are added to the end of the blob. Max size is 195 GB.
## Data protection:
- Point-in-time restore: used to restore container(s) to an earlier state. If point-in-time restore feature is enabled, then the versioning, change feed, and blob soft delete feature must also be enables to keep track of the changes.
- Soft delete: enables you to recover blobs that were previously marked for deletion, including blobs that were overwritten, for 7 days by default.
- Versioning: automatically maintain previous versions of your blobs for recovery and restoration.
## Managing metadata and security settings for storage accounts:
- Configure RBAC for resources also can help with accessing Azure storage account, this is an admin key that provides high-level access, and it is security risks when exposed.
- The safer option is generating SAS tokens and leveraging them to connect from code or script.
- Microsoft recommends using Azure Key Vault to store connection information, apply the principle the least privilege.
## Encryption
- Azure storage default use Azure Storage Service Encryption (SSE). Encryption affects performance slightly but protects your data storage in Azure Blob service. SSE is the only available and recommended option for encrypting storage accounts. 
- For encryption in transit, you have the option HTTP and HTTPS, only force an HTTPS connection with Azure certificate. The HTTPS option is not applied when you use a custom domain name registered with your storage account.
## Metadata and tags
- Metadata allows you to store company specific data for your files, such as departments names and owner contacts. This data can be retrieved without downloading blobs programmatically, which reduce charges and improves the performance of search tasks. The blob context and its metadata can be indexed using Azure Cognitive Services.
- Searching through blobs and their metadata requires using the indexing services explicitly provisioned by Azure.
- Searching though indexed tags of blobs is provided by Azure Storage service.
## Life cycle management and optimizing costs
- You can create life cycle policy rules to apply the objects within your storage account and automatically move your blobs from 1 layer or from 1 type of storage to another.
- In the Premium account, you can only delete files.
## Charges:
- Capacity
- Transactions
- Data transfer
## Notes
- For HTTPS access, Azure CDN must be used.
- You plan to use a shared access signature to protect access to services within a general-purpose v2 storage account.
	- Use `Blob`, the blob service, is the only one that supports user delegation shared access signature.
	- The file service supports account and service shared access signatures.
	- The queue service supports account and service share access signatures.
	- The table service supports account and service shared access signatures.