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