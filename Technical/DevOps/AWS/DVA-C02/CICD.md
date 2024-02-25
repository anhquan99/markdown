# CodeCommit
- GitHub alike.
## Security
- Interaction are done using Git (standard).
- Encryption:
	- Repository are automatically encrypted at rest using AWS KMS.
	- Encrypted in transit.
- Cross-account access:
	- Use an IAM Role in your AWS account and use AWS STS.
# CodePipeline
- Visual Workflow to orchestrate your CICD.
- **Source** - CodeCommit, same as GitHub.
- **Build** - CodeBuild, same as Jenkins.
- **Test** - CodeBuild.
- **Deploy** - CodeDeploy.
- **Invoke** - Lambda.
- Can use CloudFormation to deploy complex infrastructure using an API:
	- `CREATE_UPDATE`: create or update an existing stack.
	- `DELETE_ONLY`: delete a stack if it exists.
### Artifacts
- Each pipeline stage can create artifacts.
- Artifacts stored in an S3 bucket and passed on to the next stage.
### Troubleshooting
- Use CW Events. Example:
	- Create events for failed pipelines.
	- Create events for cancelled stages.
- If CodePipeline fails a stage, your pipeline stops, and you can get information in the console.
- AWS CloudTrail can be used to audit AWS API calls.
# CodeBuild
- Compile source code, run tests, produce software packages, ...
- Charged per minute for compute resources.
- Leverages Docker under the hood for reproducible builds.
- Use prepackaged Docker images or create your own custom Docker image.
- **Build instructions** file `buildspec.yml` or insert manually in Console.
- Use CW Metrics to monitor build statistics.
- Use EventBridge to detect failed build and trigger notifications.
- Use CW Alarms to notify if you need thresholds for failures.
## `buildspec.yml`
- Must be at the root of your code.
- `env` define env variables:
	- `variables` - plaintext variables.
	- `parameter-store` - variables stored in SSM Parameter Store.
	- `secrets-manager` - variables stored in AWS Secrets Manager.
- `phases` - specify commands to run:
	- `install` - install dependencies you may need for your build.
	- `pre_build` - final commands to execute before build.
	- `Build` - build command.
	- `post_build` - finishing touches.
- `artifacts` - what to upload to S3.
- `cache` - files to cache to S3 for future build speed up.
## Local build
- You can run CodeBuild locally on your desktop in case of need of deep troubleshooting beyond logs.
## Inside VPC
- By default, your CodeBuild containers are launched outside your VPC, therefore you must specify VPC configuration:
	- VPC ID
	- Subnet IDs
	- Security Groups IDs
# CodeDeploy
- Deploy new applications versions to EC2 instances, On-premises servers, Lambda functions, ECS Services.
- Automated rollback capability in case of failed deployments, or trigger CW Alarm.
- Gradual deployment control.
- A file named `appspec.yml` defines how the deployment happens.
- Perform in-place deployments or blue/green deployments.
- Must run the **CodeDeploy Agent** on the target instances.
- Define deployment speed:
	- `AllAtOnce`
	- `HalfAtATime` reduced capacity by 50%
	- `OneAtATime` slowest, lowest availability impact
	- `Custom` define your %
- Life cycle event hooks
	- **Auto scaling launch deployments:**
	  ![[Pasted image 20240224215909.png]]
	- **Auto scaling termination deployments:**
	  ![[Pasted image 20240224215925.png]]
	- **In-place deployment:**
	  ![[Pasted image 20240224215937.png]]
	- **Blue/green deployment:**
	  ![[Pasted image 20240224215826.png]]
## Lambda platform
- Automate traffic shift for Lambda aliases.
- Liner: grow traffic every N minutes until 100%.
- Canary: try X percent, then 100%.
- AllAtOnce: immediate.
## ECS platform
- Only blue/green deployments.
## EC2
- Define how to deploy the application using `appspec.yml` + deployment strategy.
- Will do in-place update to your fleet of EC2 instances.
- Can use hooks to verify the deployment after each deployment phase.
## ASG
- In-place deployment:
	- Update existing EC2 instances.
	- Newly created EC2 instances by an ASG will also get automated deployments.
- Blue/green deployment:
	- A new ASG is created (copy).
	- Choose how long to keep the old EC2 instances (old ASG).
	- Must be using ELB.
## Redeploy and rollbacks
- If a rollback happens, CodeDeploy redeploys the last known good revision as a new deployment (not a restored version).
## Troubleshooting
- Deployment Error `InvalidSignatureException - Signature expired`
	- CodeDeploy requires accurate time references.
	- If date and time on your EC2 instances are not set correctly, they might not match the signature date of your deployment request, which CodeDeploy rejects.
- Check log file to understand deployment issues
	- Linux, find at `/opt/codedeploy-agent/deployment-root/deployment-logs/codedeloy-agent-deployments.log`.
# CodeArtifact
- Storing and retrieving dependencies is called artifact management.
- Developers and CodeBuild can retrieve dependencies straight from CodeArtifact.
## Resource policies
- Used to authorize another account to access CodeArtifact.
- A given principal can either read all the packages in a repository or none of them.
## Upstream repositories
- A CodeArtifact repo can have other CodeArtifact repos as Upstream repo, which allows a package manager client to access the packages that are contained in more than 1 repo using a single repo endpoint.
- Up to 10 upstream repos.
- Only 1 external connection.
## External connection
- Is a connection between a CodeArtifact repo and an external/public repo, which allows fetching packages that are not already in your repo.
## Retention
- If a request package version is found in an Upstream repo, a reference to it is retained and always available from the downstream repo, not the intermediate repo.
## Domains
- **Deduplicated storage**: asset only needs to be stored once in a domain, even if it's available in many repos (only pay once for storage).
- **Fast copying**: only metadata record are updated when you pull packages from an upstream repo into downstream.
- **Easy sharing across repos and team.**
- **Apply policy across multiple repos**: domain admin can apply policy across the domain.
# CodeGuru
- An ML-powered service for automated code reviews and application performance recommendations.
## Reviewer
## Profiler
- Features:
	- Identify and remove code inefficiencies.
	- Improve application performance.
	- Decrease compute costs.
	- Provides heap summary (identify which objects using up memory).
	- Anomaly detection.
- Minimal overhead on application.
## Agent configuration
- `MaxStackDepth`
- `MemoryUsageLimitPercent`
- `MinimumTimeForReportingInMilliseconds`
- `ReportingIntervalInMiliseconds`
- `SamplingIntervalInMilliseconds`
# Cloud9
- Cloud based IDE.