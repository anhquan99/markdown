[[Technical/DevOps/Docker/Container|Container]]
# Container management
- Elastic Container Service (ECS)
	- Amazon's own container platform.
- Elastic K8s Service (EKS)
	- Managed K8s.
- Fargate
	- Amazon's own Serverless container platform.
	- Works with ECS and EKS.
- Elastic Container Registry (ECR)
	- Store container images.
# Launch types
## EC2
- Launch Docker container on AWS = launch ECS Tasks on ECS Clusters.
- When launching the container with EC2, it must run the ECS Agent to register in the ECS Cluster therefore you must provision and maintain the infrastructure (EC2 instance).
- AWS takes care of starting/stopping containers.
## Fargate
- Serverless.
- You just create task definitions and AWS runs ECS Tasks for you based on the CPU/RAM you need.
- To scale, just increase the number of tasks.
# IAM Roles for ECS
## EC2
- Used by ECS agent.
- Makes API calls to ECS service.
- Send container logs to CloudWatch Logs.
- Pull Docker image from ECR.
- Reference sensitive data in Secrets Manager or SSM Parameter Store.
## ECS Task Role
- Allows each task to have a specific role.
- Use different roles for the different ECS Services you run.
- Task Role is defined in the task definition.
# Load Balancer integration
- ABL supported and works for most use cases.
- NLB recommended only for high throughput/performance use cases, or to pair it with AWS Private Link.
- CLB supported but not recommended (no Fargate).
# Elastic File System (EFS) - Data volumes
- Mount EFS file systems onto ECS tasks.
- Works for both EC2 and Fatgate.
- Tasks running in any AZ will share the same data in EFS.
- Fargate + EFS = Serverless.
- Used for persistent multi AZ for storage containers.
- S3 can not be mounted as a file system.
# Auto scaling
## ECS Service 
- Automatically increase/decrease the desired number of ECS tasks.
- ECS Auto Scaling uses AWS Application Auto Scaling.
	- Average CPU Utilization.
	- Average Memory Utilization - Scale on RAM.
	- Request count per target - metric coming from ALB.
- **Target tracking:** scale based on target value for specific CloudWatch metric.
- **Step Scaling:** scale based on specified CloudWatch Alarm.
- **Scheduled Scaling:** scale based on a specified data/time (predictable changes).
## EC2 instances
- Accommodate ECS Service Scaling by adding underlying EC2 instances.
- ASG
	- Scale your ASG based on CPU Utilization.
	- Add EC2 instances over time.
- ECS Cluster Capacity Provider
	- Used to automatically provision and scale the infrastructure for your ECS Tasks.
	- Capacity Provider paired with an ASG.
	- Add EC2 instances when you're missing capacity (CPU, RAM).