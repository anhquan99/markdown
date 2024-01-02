# Buckets
- Allows people to store object (files) in buckets (directory).
- Buckets must have a globally unique name (across all regions all accounts) and are defined at the region level.
- Objects (files) have a key, which is the full path of the objects. Because there is no concept of directories in S3, the key will trick you to think like it.
- Max object size is 5TB, and if more than 5GB, user must use **multipart upload**.
## Security
- User based: use with IAM policies.
- Resource based:
	- Bucket policies: allows cross account.
	- Object ACL
	- Bucket ACL
- IAM principal can access an S3 object if:
	- The user IAM permissions allow it or the resource policy allows it.
	- And there is no explicit deny.
- Can be set at the account level.
## Static website hosting
- Website URL is depending on the region:
	- `http://bucket-name.s3-website-aws-region.amazonaws.com`
	- `http://bucket-name.s3-website.aws-region.amazonaws.com`
- Make sure to allow public read when encounter 403 code.
## Versioning
- Enabled at the bucket level.
- It is best practice to version your buckets:
	- Protect against unintended deletes (ability to restore a version).
	- Easy roll back to previous version.
- Notes:
	- Any file that is not versioned prior to enabling versioning will have version "null".
	- Suspending versioning does not delete the previous versions.
## Replication
- Must enable versioning in source and destination buckets.
- Cross Region Replication (CRR) and Same Region Replication (SRR).
- Must give proper IAM permissions to S3.
- Copying is asynchronous.
- Use cases:
	- CRR: compliance, lower latency access, replication across.
	- SRR: log aggregation, live replication between production and test accounts.
- Notes:
	- After replication is enabled, only new objects are replicated.
	- You can use S3 Batch Replication to replicates existing objets and objects that failed replication.
	- Can replicate delete markers from and not replicate object with a version ID.
	- There is not chaining of replication that means A replicates to B and B replicates C, A not replicates to C.
## Storage class
- Standard - General purpose
	- Used for frequently accessed data.
	- Low latency and high throughput.
	- Sustain 2 concurrent facility failures.
	- Use cases: big data analytics, mobile and gaming applications, content distribution, ...
- Standard-Infrequent-Access (IA)
	- Less frequently accessed, but requires rapid access when needed.
	- Lower cost than standard.
	- Use cases: disaster recovery, backups.
- One Zone-Infrequent Access
	- 99.5% availability.
	- Data lost when AZ is destroyed.
	- Use cases: storing secondary backup copies of on-premises data, or data you can recreate.
	-  Low cost object storage meant for archiving/backup.
	- Pricing: price for storage + object retrieval cost.
- Glacier Instant Retrieval
	- Millisecond retrieval, great for data accessed once a quarter.
	- Minimum storage duration of 90 days.
- Glacier Flexible Retrieval
	- Expedited (1 to 5 minutes), standard (3 to 5 hours), bulk (5 to 12 hours) - free.
	- Minimum storage duration of 90 days.
- Glacier Deep Archive
	- For long term storage.
	- Standard (12 hours), bulk (48 hours).
	- Minimum storage duration of 180 days.
- Intelligent Tiering
	- Small monthly monitoring and auto-tiering fee.
	- Moves object automatically between Access Tiers based on usage.
	- There are no retrieval charges in Intelligent-Tiering.
	- Frequent Access tier (automatic): default tier.
	- Infrequent Access tier (automatic): objects not accessed for 30 days.
	- Archive Instant Access tier (automatic): objects not accessed for 90 days.
	- Archive Access tier (optional): config from 90 days to 700+ days.
	- Depp Archive Access tier (optional): config from 180 days to 700+ days.
## Durability
- High durability and same for all storage classes.
# Lifecycle rules
- Automate moving objects.
### Rules
- Transition actions: configure objects to transition to another storage class.
- Expiration action: configure objects to expire (delete) after some time.
	- Access log files can be set to delete after 365 days.
	- Can be used to delete old versions of files (if versioning is enabled).
	- Can be used to delete incomplete Multi-Part uploads.
- Apply rules for prefix and tags.
# Storage class analysis
- Help you decide when to transition objects to the right storage class.
- Recommendations for Standard and Standard IA, does not work for One-Zone IA or Glacier.
- 24 to 48 hours to start seeing data analysis.
# Event notification
- Send to SNS, SQS, EventBridge, lambda function, ...
- Deliver in seconds, but sometimes take a minute.
# Baseline performance
- Auto scales to high request rates, latency 100-200 ms.
- At least 3500 PUT/COPY/POST/DELETE or 5500 GET/HEAD requests per second per prefix in a bucket.
- There are no limits to the number of prefixes in a bucket.
### Multi-Part upload
- Recommended for files > 100MB, must use for files > 5GB.
- Can help parallelize uploads.
### Transfer acceleration
- Transfer file to AWS edge location and from there forward to S3 bucket in the target location by using AWS private network.
- Compatible with multipart upload
### S3 byte-range fetches
- Parallelize GETs by requesting specific byte ranges.
- Better resilience in case of failures.
- Can be used to retrieve only partial data (like get header of a file).
### Select and Glacier select
- Retrieve less data using SQL by performing **server-side filtering**.
- Can filter by rows and columns.
- Less network transfer, less CPU cost client-side.
# User defined
- You can not search the object metadata or object tags, instead you must use an external DB as a search index like DynamoDB.
## Object metadata
- Assign a custom metadata with name-value and name begin with `x-amz-meta-`.
## S3 object tags
- Key-value pairs for objects.
- Used for fine-grained permissions and useful for analytics purposes.
# Security
- Server side encryption (SSE)
	- S3-Managed Keys (SSE-S3) enable by default, handled, managed and owned by AWS.
		- Encryption type is AES-256.
		- Must set header `"x-amz-server-side-encryption": "AES256"`.
		- Enabled by default.
	- KMS Keys stored in AWS KMS (SSE-KMS) leverage AWS Key Management Service (AWS KMS) to manage encryption keys.
		- KMS advantages: user control + audit key usage using CloudTrail.
		- Must set header `"x-amz-server-side-encryption": "aws:kms"`.
		- Limitation:
			- Limit by the KMS limits.
			- When uploading, S3 calls the `GenerateDataKey` KMS API, and when downloading, S3 calls `Decrypt` KMS API.
			- Can increase quota by request service ticket.
	- Customer-Provided Keys (SSE-C) bring your own key.
		- HTTPS must be used.
		- Encryption keys must be provided in HTTP headers for every request made.
- Client side encryption
	- Client must handle data themselves before sending to and after downloading from S3.
- Encryption in transit (SSL/TLS).
- Force encryption in transit, `aws:SecureTransport`.
- Bucket policies are evaluated before `Default Encryption`.
- MFA delete on S3 
	- Required to:
		- Permanently delete an object version.
		- Suspend Versioning on the bucket.
	- Not required to:
		- Enable Versioning.
		- List deleted versions.
	- Only root account can enable/disable on bucket.
# Access logs
- Do not set your logging bucket to be the monitored bucket, it will create a logging loop, and your bucket will grow exponentially.
# Pre-signed URLs
- Using S3 Console, AWS CLI or SDK.
- Expiration:
	- S3 Console: 1 min up to 720 mins (12 hours).
	- AWS CLI: config with `--expires-in` parameter in seconds, default 3600 secs, max 604800 secs (168 hours).
# Access points
- Simplify security management for S3 buckets.
- Each Access Point has:
	- DNS name (Internet Origin or VPC Origin).
		- For the VPC Origin, first create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint) then VPC Endpoint Policy must allow access to the target bucket and Access Point.
	- Access point policy (similar to bucket policy) to manage security at scale.
#  Object lambda
- Use AWS Lambda Functions to change the object before it is retrieved by the caller application.
- Create S3 Access Point and S3 Object Lambda Access Points on top of S3.
