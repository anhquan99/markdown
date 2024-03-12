- Deploy applications in AWS safely and predictably.
- Elastic Beanstalk is a developer centric view of deploying an application on AWS. It uses all the component in AWS.
- Managed service
	-  Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration, ...
	- Just the application code is the responsibility of the developer.
- Free, but you pay for the underlying instances.
- Relies on CloudFormation.
# Components
- Application: collection of Elastic Beanstalk components.
- Application Version: an iteration of your application code.
- Environment:
	- Collection of AWS resources running an application version (only 1 application version at a time).
	- Tiers: web server environment tier and worker environment tier.
	- Multiple environment.
# Permissions
- **Service role:** is the IAM role that Elastic Beanstalk assumes when calling other services on your behalf.
- **Instance profile:** is an IAM role that's applied to Amazon EC2 instances that are launched in your Elastic Beanstalk environment.
- **User policy:** create IAM user for each user who uses Elastic Beanstalk to avoid using your root account for sharing credentials.
# Deployment options for updates
### All at once
- Deploy all in 1 go
- Fastest deployment, has downtime and no cost.
- Great for dev env.
### Rolling
- Update a few instances at a time (bucket - can be set size), and then move onto the next bucket once the first bucket is healthy.
- The application is running below capacity with both versions simultaneously.
- No cost, long deployment.
### Rolling with addition batches
- Like rolling, but spins up new instances to move the batch (so that the old application is still available).
- The application is running at capacity with both versions simultaneously.
- Small additional cost because the system must add an additional instance to ensure the application is running at capacity, long deployment.
- Great for production.
### Immutable
- Spins up new instances in a new ASG, deploys version to these instances, and then swaps all the instances when everything is healthy.
- Zero downtime, high cost, double capacity, the longest deployment.
- New version is deployed on a temporary ASG.
- Quick rollback in case of failures.
- Great for production.
### Blue-green
- Create a new env and switch over when ready.
- Not a direct feature of Elastic Beanstalk.
- Zero downtime and release facility.
- Create a new stage env and deploy new version there (green), it can be validated independently and roll back if issues. Route 53 can be setup using weighted policies to redirect a little bit of traffic to the stage env.
- Using Beanstalk, swap URLs when done with the env test.
### Traffic splitting
- Canary testing - send a small % of traffic to new deployment.
- Like blue-green.
### Blue/Green deployments
- Create separate, but identical environment. One environment (blue) is running the current application version and one environment (green) is running the new application version.
- Using a blue/green deployment strategy increases application availability and reduces deployment risk by simplifying the rollback process if a deployment fails.
# Lifecycle policy
- Elastic Beanstalk can store at most 1000 application versions, you can not deploy if the number of version is exceeded unless you remove the old versions.
- Lifecycle policy
	- Based on time
	- Based on space
- Option not to delete the source bundle in S3 to prevent data loss.
# Extensions
- Zip file containing the code must be deployed to Elastic Beanstalk.
- All the parameters set in the UI can be configured with code using files.
- Requirements:
	- Must have the `.ebextensions` directory in the root of source code.
	- YAML/JSON.
	- `.config` extensions.
	- Able to modify some default settings using: option_settings.
	- Ability to add resources such as RDS, ElasticCache, ...
- Resources managed by `.ebextensions` get deleted if the env goes away.
# Cloning
- Clone an env with exact same configuration.
- All resources and configuration are preserved:
	- Load Balancer type and configuration.
	- RDS database type (not the data).
	- Env variables.
### Migration load balancer
- After creating an Elastic Beanstalk env, you can't change the ELB type (only the config)
- To migrate load balancer:
	1. Create a new env with the same config except LB.
	2. Deploy application to the new env.
	3. Perform a CNAME swap or Route 53 update.
### RDS
- Great for dev/test but not for production. Because it is tied to the lifecycle of the Beanstalk.
- The best for prod is to separately create an RDS db and provide the application with connection string.
- Decouple RDS:
	1. Create a snapshot of RDS db (as a safeguard).
	2. Go to the RDS console and protect the RDS database from deletion.
	3. Create a new Elastic Beanstalk env, without RDS, point your application to existing RDS.
	4. Perform a CNAME swap or Route 53 update.
	5. Terminate the old env (RDS will not be deleted).
	6. Delete CloudFormation stack.