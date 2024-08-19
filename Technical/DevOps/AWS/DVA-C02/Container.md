[[Technical/DevOps/Docker/Container|Container]]
# Container management
- Elastic Container Service (ECS)
	- Amazon's own container platform.
- Elastic K8s Service (EKS)
	- Managed K8s.
- Fargate
	- Amazon's own Serverless container platform.
	- Works with ECS and EKS.
	- Scenarios:
		- Large workloads that require low operational overhead.
		- Small workloads that have occasional burst.
		- Tiny workloads.
		- Batch workloads.
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
# Data volumes
## Elastic File System (EFS)
- Mount EFS file systems onto ECS tasks.
- Works for both EC2 and Fatgate.
- Tasks running in any AZ will share the same data in EFS.
- Fargate + EFS = Serverless.
- Used for persistent multi AZ for storage containers.
- S3 can not be mounted as a file system.
## Bind mounts
- Share data between multiple containers in the same Task Definition.
- Work for EC2 and Fargate tasks.
- EC2 tasks are using EC2 instance storage - tied to the lifecycle of the instance.
- Fargate Tasks are using ephermeral storage - tied to the container(s) using them, size from 20GB - 200GB (default 20GB).
- Used to share data between multiple containers, sidecar container pattern where the sidecar container used to send metrics/logs to other destinations.
# Autoscaling
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
# Rolling updates
- When updating from v1 to v2, we can control how many tasks can be started and stopped, and in which order.
	- Min healthy percent.
	- Max healthy percent.
	- Allowed to terminate tasks: terminate task to replace new v2.
	- Allowed to create tasks: create new task to ensure the healthy percent is reserved.
# Task definitions
- They are metadata in JSON form to tell ECS how to run a Docker container.
- It contains crucial information:
	- Image Name
	- Port Binding for Container and Host
	- Memory and CPU required
	- Env variables
	- Networking information
	- IAM Role
	- Logging configuration
- Can define up to 10 containers in a Task Definition.
# Load balancing
## EC2
- Dynamic Host Port Mapping if you define only the container port in the task definition.
- The ALB finds the right port on your EC2 instances.
- You must allow on the EC2 instance's Security Group any port from the ALB's Security Group.
## Fargate
- Each task has a unique private IP.
- Only define the container port.
# Task placement
- When an ECS task is started with EC2 Launch Type, ECS must determine where to place it, with the constraints of CPU and memory (RAM). Similarly, ECS need to determine which task to terminate.
- You can only define for EC2 Launch Type:
	- Task Placement Strategy.
	- Task Placement Constraints.
## Task Placement Process
- Task Placement Strategies are the best effort.
- Process
	1. Identify which instances that satisfy the CPU, memory, and port requirements.
	2. Identify which instances that satisfy the Task Placement Constraints.
	3. Identify which instances that satisfy the Task Placement Strategies.
	4. Select the instances.
## Task Placement Strategies
- You can mix strategies together.
### Binpack
- Tasks are placed on the least available amount of CPU and memory.
- Minimizes the number of EC2 instances in use (cost savings).
### Random
- Tasks are placed randomly.
### Spread
- Tasks are placed evenly based on the specified value.
- Example: instanceId, attribute:ecs.availability-zone, ...
## Task Placement Constraints
### distinctInstance
- Tasks are placed on a different EC2 instance.
### memberOf
- Tasks are placed on EC2 instances that satisfy a specified expression.
- Uses the Cluster Query Language.
# AWS Copilot
- CLI tool to build, release, and operate production-ready containerized apps.
- Helps you focus on building apps rather than setting up infrastructure.
- Provisions all required infrastructure for containerized apps.
- Automated deployments with one command using CodePipeline.
- Deploy to multiple env.
- Troubleshooting, logs, health status, ...
# EKS
- Supports EC2 to deploy worker nodes or Fargate to deploy serverless containers.
## Node types
- Managed node groups
	- Creates and manages Nodes (EC2) for you.
	- Nodes are part of ASG managed by EKS.
	- Supports On-Demand or Spot Instances.
- Self-Managed node
	- Nodes created by you and registered to the EKS cluster and managed by ASG.
	- You can use prebuilt AMI - Amazon EKS optimized AMI.
	-  Supports On-Demand or Spot Instances.
- AWS Fargate
	- No maintenance required, no nodes managed.
## Data volumes
- Need to specify **StorageClass** manifest on your EKS cluster.
- Leverages a Container Storage Interface (CSI) compliant driver:
	- EBS
	- EFS
	- FSx For Lustre
	- FSx for NetApp ONTAP
# ECS
## Terminology and components
- Layers:
	- Capacity: the infrastructure where your containers run.
	- Controller: deploy and manage your applications that run on the containers.
	- Provisioning: the tools you can use to interface with the scheduler to deploy and manage your application containers.
	  ![[Pasted image 20240308080029.png]]
	  ![[Pasted image 20240308080108.png]]
- Task definition: is like a blueprint for your application. Each time you launch a task in Amazon ECS, you specify a task definition. The service then knows which Docker image to use for containers, how many containers to use in the task, and the resource allocation for each container.
```ad-note
- You have a containerized application stored as Docker images in an ECR repository, that you want to run on an ECS cluster. You're trying to launch two copies of the same Docker container on the same EC2 container instance. The first container successfully starts, but the second container doesn't. You have checked that there's enough CPU and RAM on the EC2 container instance. What is the problem here?
	- The host port defined in the task definition.
	- To enable random host port, set host port = 0 (or empty), which allows multiple containers of the same type to launch on the same EC2 container instance.
```