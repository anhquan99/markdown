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
#  AWS Certificate Manager (ACM)
- Let you easily provision, manage, and deploy SSL/TLS certificates.
- Support both public and private TLS certificates.
- Automatic TLS certificate renewal.
# Private Certificate Authority (CA)
- Managed service allows you to create private Certificate Authorities (CA), including root and subordinaries CAs.
- Certificates are trusted only by your Organization.
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