# Simple Email Service (SES)
- Fully managed service to send emails securely, globally and at scale.
- Allows inbound/outbound emails.
- Reputation dashboard, performance insights, anti-spam feedback.
- Supports DomainKeys Identified Mail (DKIM) and Sender Policy Framework (SPF).
# OpenSearch Service
- Is the successor to Amazon ElasticSearch.
- You can search any field, even partially matches.
- It's common to use OpenSearch as a complement to another database.
- 2 modes: managed cluster or serverless cluster.
# Athena
- Amazon Athena is an interactive query service that makes it easy to analyze data directly in S3 using standard SQL.
- Serverless query service to analyze data stored in S3.
- Used for Business intelligence / analytics / reporting, ...
## Performance improvement
- Use columnar data for cost-saving (less scan)
- Compress data for smaller retrievals.
- Partition datasets in S3 for easy querying on virtual columns.
- Use larger files (> 128 MB) to minimize overhead.
# Managed Streaming for Apache Kafka (MSK)
- Alternative to Kinesis.
- Fully managed Apache Kafka on AWS.
- MSK serverless.
# Certificate manager
- Best practices:
	- Documenting CA structure and policies.
	- Minimize use of the root CA if possible.
	- Give the root CA its own AWS account.
	- Separate administrator and issuer roles.
	- Implement managed revocation of certificates.
	- Turn on CloudTrail.
	- Rotate the CA private key.
	- Delete unused CAs.
	- Block public access to your CRLs.
##  AWS Certificate Manager (ACM)
- Let you easily provision, manage, and deploy SSL/TLS certificates.
- Support both public and private TLS certificates.
- Automatic TLS certificate renewal.
- Used for enterprise customers who need a publicly trusted secured web presence using TLS.
## Private Certificate Authority (CA)
- Managed service allows you to create private Certificate Authorities (CA), including root and subordinaries CAs.
- Certificates are trusted only by your Organization.
- To use an ACM certificate with CloudFront, you must request or import the certificate in the US East region.
## CA hierarchy
- Up to 5 levels.
- Benefits:
	- Granular security controls appropriate to each CA.
	- Division of administrative tasks for better load balancing and security.
	- Use of CAs with limited, revocable trust for daily operations.
	- Validity periods and. certificate path limits.
	  ![[Pasted image 20240311071632.png]]
- Validate end-entity certificates: end-entity certificates derive their trust from a certification path leading back through the subordinate CAs to a root CA. When a web browser or other client is presented with an end-entity certificate, it attempts to construct a chain of trust. For example, it may check to see that the certificate's _issuer distinguished name_ and _subject distinguished name_ match with the corresponding fields of the issuing CA certificate. Matching would continue at each successive level up the hierarchy until the client reaches a trusted root that is contained in its trust store.
  ![[Pasted image 20240311072226.png]]
# Macie
- Data security and data privacy service that use machine learning and pattern matching to discover and protect your sensitive data in AWS.
- Identifies and alert you to sensitive data, such as personal information.
# AppConfig
- Configure, validate, and deploy dynamic configurations.
- Deploy dynamic configuration changes to your applications independently of any code deployments.
- Feature flag, application, tuning, allow/block listing, ...
> [!info] Note
> AppConfig is a capability of System Manager
# CloudWatch Evidently
- Safely validate new features by serving them to a specified % of your users.
- **Launches** (= feature flags).
- **Experiments** (= A/B testing) compare multiple versions of the same feature.
- **Overrides** - pre-define a variation for a specific user.