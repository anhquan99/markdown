# SDK
## AWS limits (Quotas)
- API rate limits
	- **DescribeInstances** API for EC2 has a limit of 100 calls per seconds.
	- **GetObject** on S3 has limit of 5500 GET per second per prefix.
	- **Intermittent Errors**: implement exponential backoff.
	- **Consistent Errors**: request an API throttling limit increase.
- Service quotas (service limits)
	- Running on-demand standard instances: 1152 vCPU.
	- You can request a service limit increase by opening a ticket.
	- You can request a service quota increase by using the Service Quotas API.
- Exponential backoff (any AWS service)
	- Implement when getting **ThrottlingExpception**, use AWS SDK API or implement your self.
	- Must only implement the retries on 5xx server errors and throttling.
# CLI
- Credential provider chain lookup order
	1. Command line options - `--region, --output, and --profile`.
	2. Environment variables `AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY AND AWS_SESSION_TOKEN`.
	3. CLI credentials file `-aws configure` saved in `~/.aws/credentials` on Linux/macOS.
	4. CLI configuration file `-aws configure` saved in `~/.aws/config` on Linux/macOS.
	5. Container credentials for ECS tasks.
	6. Instance profile credentials for EC2 Instance Profiles.
- Using credentials scenario:
	- IAM user has S3FullAccess permissions.
	- An application deployed on an EC2 instance using env variables with credentials from an IAM user to call the S3 API.
	- Application only uses 1 S3 bucket:
		- An IAM Role and EC2 instance profile was created for the EC2 instance.
		- The role was assigned the minimum permissions to access that 1 S3 bucket.
- Credentials best practices:
	- Never store AWS credentials in code.
	- Inherited from the credentials chain.
	- If using working within AWS, use IAM roles:
		- EC2 instance roles for EC2 instances.
		- ECS roles for ECS tasks.
		- Lambda roles for lambda functions.
	- If working outside of AWS, use env variables/named profiles.
- You should sign an AWS HTTP request using Signature v4 (SigV4).