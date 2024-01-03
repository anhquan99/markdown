- It is a Content Delivery Network.
- DDoS protection, integration with Shield, AWS Web Application Firewall (WAF).
# Origins
## S3 bucket
- Distribute files and cache them at the edge.
- Enhanced security with CloudFront Origin Access Control (OAC).
- Can be used as an ingress (to upload files to S3).
## Custom Origin (HTTP)
- ALB
- EC2 instance
- S3 website
- Any HTTP backend
# Compare with other services
## CloudFront:
- Global edge network.
- Files are cached for a TTL.
- Great for static content that must be available everywhere.
## S3 cross region replication 
- Must be setup for each region you want to replicate.
- Files are updated in near real-time.
- Read only.
- Great for dynamic content that needs to be available at low-latency in few regions.
# Caching
- Invalid part of the cache using the `CreateInvalidation` API.
- Cache key
	- Is a unique identifier for every object in the cache. By default, consists of hostname + resource portion of the URL.
	- For applications that serve content that varies based on user, device, language, location, ...
	- You can add other elements (HTTP headers, cookies, query string) to the Cache Key using CloudFront Cache Policies.
### Policy
- Based on:
	- HTTP headers: None - Whitelist.
	- Cookies: None - Whitelist - Include All - Except - All.
	- Query Strings: None - Whitelist - Include All - Except - All.
- TTL can be set by the origin using the Cache-Control header, Expires header, ...
- You can create your own policy or use Predefined Managed Policies.
- All HTTP headers, cookies, and query strings that you include in the Cache Key are automatically included in origin requests.
#### HTTP headers:
- None:
	- Don't include any headers in the Cache Key (except default).
	- Headers are not forwarded (except default).
	- Best caching performance.
- Whitelist:
	- Only specified headers included in the Cache Key and forwarded to Origin.
#### Query Strings
- None: same as HTTP
- Whitelist: same as HTTP
- Include All-Except:
	- Include and forward all query strings except the specified list.
- All: include all, worst caching performance
#### Origin request policy
- Specify values that you want to include in origin requests without including them in the Cache Key (no duplicated cached content).
- Include:
	- HTTP headers: None - Whitelist - All viewer headers options
	- Cookies: None - Whitelist - All
	- Query Strings: None - Whitelist - All
- Ability to add CloudFront HTTP headers and Custom Headers to an origin request that were not included in the viewer request.
# Cache invalidation
- Purge cache with CloudFront Invalidation with all files (\*) or special path (/images/\*)
# Behaviors
- Configure different settings for a given URL path pattern.
- Route to different kind of origin(s) groups based on the content type or path pattern (multiple origin)
	- /images/*
	- /api/*
	- /* (default cache behavior, last to be processed and is always /\*)
- You can maximize cache hits by separating static and dynamic distributions.
# Geo restriction
- Restrict who can access your distribution:
	- Allowlist: allow users in the allowed countries to access content.
	- Blocklist.
- The country is determined using a third party Geo-IP database.
- Use case: copyright laws to control access to content.
# Signed URL / signed cookies
- Attach a policy with:
	- Includes URL expiration.
	- Includes IP ranges to access the data from.
	- Trusted signers (which AWS accounts can created signed URLs).
- Valid time:
	- Shared content (movie, music): short (minutes).
	- Private content (private to the user): long (years).
- Singed URL: access to individual files (per file per URL).
- Singed cookies: access to multiple files.

| CloudFront Signed URL | S3 Pre-Signed URL |
| --- | --- |
| Allow access to a path, no matter the origin | Issue a request as the person who pre-signed the URL |
| Account wide key-pair, only the root can manage it | Uses the IAM key of the signing IAM principal |
| Can filter by IP, path, date, expiration | Limited lifetime |
| Can leverage caching features | |
### Process
- Type of signers:
	- Trusted key group (recommended): can leverage APIs to create and rotate keys (and IAM for API security).
	- AWS account that contains a CloudFront Key Pair
		- Need to managed keys using the root account and the AWS console.
		- Not recommended because it is using root account.
- Public key is used by your applications to sign URLs.
- Private key (uploaded) is used by the CloudFront to verify URLs. 
# Price classes
- All: all region - best performance.
- 200: excludes most expensive regions.
- 100: only the least expensive regions.
# Origin groups
- To increase high-availability and do failover.
- 1 primary and 1 secondary origin.
# Field level encryption
- Protect user sensitive information through application stack.
- Adds an additional layer of security along with HTTPS.
- Sensitive information encrypted at the edge close to user.
- Uses asymmetric encryption.
# Real time logs
- Real-time requests are sent to Kinesis Data Streams.
- Monitor, analyze and take actions based on content delivery performance.
- Allows you to choose:
	- Sampling rate: percentage of requests for which you want to receive.
	- Specific fields and specific Cache Behaviors (path patterns).