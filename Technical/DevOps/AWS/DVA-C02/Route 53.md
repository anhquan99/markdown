- Highly available, scalable, fully managed and authoritative DNS (the customer can update the DNS records).
- The only AWS service provides 100% available SLA.
## DNS terminologies
- Domain registrar: Route 53, GoDaddy, ...
- DNS records: A, AAAA, CNAME, NS, ...
- Zone File: contains DNS records.
- Name Server: resolves DNS queries (Authoritative or None-Authoritative).
- Top Level Domain (TLD): .com, .us, .vn, ...
- Second Level Domain (SLD): amazon.com, google.com, ...
## Records
- How you want to route traffic for a domain.
- Each domain contains:
	- Domain/subdomain name.
	- Record type
		- A - maps a hostname to IPv4.
		- AAAA - maps a hostname to IPv6.
		- CNAME - maps a hostname to another hostname.
			- The target is a domain name which must have an A or AAAA record.
			- Can't create a CNAME record for the top node of a DNS namespace (Zone Apex).
			- Example: you can't create for `example.com`, but you can create for `www.example.com`
		- NS - name servers for the hosted zone
			- Control how traffic is routed for a domain.
	- Value
	- Routing policy
	- TTL
- Route 53 supports DNS record types:
	- A / AAAA / CNAME / NS
	- CAA / DS / MX / NAPTR / PTR / SOA / TXT / SPF / SRV
## Hosted zones
- A container for records that define how to route traffic to a domain and its subdomains.
- Public hosted zone.
- Private hosted zone: contain records that specify how you route traffic within one or more VPCs (private domain names).
## Alias records
- Points a hostname to an AWS resource.
- Works for ROOT DOMAIN and NON ROOT DOMAIN (aka mydomain.com).
- Free of charge.
- Native health check.
- Auto recognizes changes in the resource's IP address.
- Unlike CNAME, it can be used for the top node of a DNS namespace (Zone Apex).
- Alias record is always of type A/AAAA for AWS resources.
- You can't set TTL.
- Targets:
	- ELB
	- CloudFront Distributions
	- API Gateway
	- Elastic Beanstalk environments
	- S3 websites
	- VPC interface endpoints
	- Global accelerator
	- Route 53 record in the same hosted zone
- You can't set an alias record for an EC2 DNS name.
## Routing policies
- Routing in DNS is query DNS.
- Policies:
	- **Simple**: 
		- Route traffic to a single resource.
		- Can specify multiple values in the same record, and the client will choose a random value.
		- Can't be associated with Heath checks.
	- **Weighted**
		- Control the % of the requests that go to each specific resource.
		- Weights don't need to sum up to 100.
		- DNS records must have the same name and type.
		- Can be associated with Health checks.
		- For load balancing between regions, testing new application versions, ...
		- Assign 0 to weight to stop sending traffic to a resource, if all weight is 0 then distribute equally.
	- **Failover**
	- **Latency based**: redirect to the resource that has the least latency.
	- **Geolocation**: routing is based on user location.
	- **Multi-value answer** (can be associated with Health checks).
	- **Geoproximity** (using Route 54 traffic flow feature): shift more traffic to resources based on the defined bias.
	- **IP based**
		- Routing based on client's IP.
		- Using a list of CIDRs (Classless Inter-Domain Routing) and mapping to the corresponding endpoints.
		- For optimize performance, reduct network costs, ...
## Health checks
- HTTP Health checks are only for public resources.
- Health checks are integrated with CW metrics.
- Types:
	- Monitor and endpoint.
	- Monitor other health check.
	- Monitor CloudWatch.
- About 15 global health checkers will check the endpoint health. If > 18% of health checkers report the endpoint is healthy, Route 53 considers it healthy.
- Pass when the status code is 2xx and 3xx. Can set up pass/fail based on the text in the first 5120 bytes of the response.
- Config router/firewall to allow incoming request from Route 53 Health checkers.
### Calculated health checks
- Combine the result of multiple Health checks into single Health checks.
- Use OR, AND, NOT.
- Up to 256 child Health checks.
- Specify how many of the health checks need to pass to make the parent pass.
- For performance maintenance to your website without causing all health checks to fail.
### Private hosted zones
- Create CW metric and associate a CW alarm, then create a Health check that checks the alarm itself.
## Traffic flow
- Simplify the process of creating and maintaining records in large and complex configuration.
- Save as traffic flow policy
	- Can apply to different route 53 hosted zones (different domain names).
	- Supports versioning.
## Domain Registar 
- You buy or register your domain name with a Domain Registrar, and it usually provides a DNS service to manage DNS records.
- Domain Registrar != DNS Service