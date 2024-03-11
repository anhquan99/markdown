- Infrastructure as a Service.
  ![[Pasted image 20240311222331.png]]
# Capability
- Renting virtual machines (EC2).
- Storing data on virtual drives
	- Network (EBS & EFS).
	- Hardware (EC2 Instance Store)
- Distributing load across machines (ELB).
- Scaling the services using an auto-scaling group (ASG).
# User data
- It is possible to bootstrap our instances using an EC2 User data script.
- Bootstrapping means launching commands when a machine starts and run only once at the instance first start.
# Instance Types
- Naming convention: <mark style="background: #ADCCFFA6;">m</mark><mark style="background: #FFB86CA6;">5</mark>.<mark style="background: #BBFABBA6;">2xlarge</mark>
	- `m`: instance class.
	- `5`: generation (AWS improves them over time).
	- `2xlarge`: size within the instance class.
## Types:
- General purpose.
- Compute optimized.
- Memory optimized.
- Storage optimized.
# Security groups
- They are the fundamental of network security in AWS, they control how traffic is allowed into or out of our EC2 instances can be used as the firewall.
- Security group only contain allow rules.
- Can be attached to multiple instances.
- Locked down to a region / VPC combination.
- Live outside EC2, if traffic is blocked the EC2 instance won't see it.
- It's good to maintain one separate security group for SSH access.
- If your application is not accessible (time out), then it's a security group issue. If your application gives a "connection refused" error, then it's an application error, or it's not launched.
## Ports
- 22 = SSH.
	- There is EC2 Instance Connect is not required port 22 to open.
- 21 = FTP.
- 22 = SFTP.
- 80 = HTTP.
- 443 = HTTPS.
- 3389 = Remote Desktop Protocol - Windows instance.
# Purchasing options
- On-demand Instances:
	- Pay as you go:
		- Linux or Windows - billing per second, after the first minute.
		- All other operating systems - billing per hour.
	- Highest cost but no upfront payment.
	- Recommended for short-term and up-interrupted workloads, where you can't predict how the application will behave.
- Reserved instances:
	- Discount 72% compared to On-demand.
	- Reserve a specific instance attributes (Instance Type, Region, Tenancy OS).
	- Recommend for steady-state usage applications (like database).
	- Convertible reserved instance: can change the EC2 instance type, instance family, OS, scope and tenancy. Up to 66% discount.
- Savings plans:
	- Discount based on long-term usage (up to 72% - same as reserved instances).
	- Usage beyond EC2 saving plans is billed at the on-demand price.
- Spot instances:
	- Discount up to 90% compared to on-demand.
	- Instances that you can lose at any point of time if your max price is less than the current spot price.
	- Useful for workloads that are resilient to failure:
		- Batch jobs.
		- Data analysis.
		- Image processing.
		- Any distributed workloads.
		- Workloads with a flexible start and end time.
- Dedicated host:
	- The most expensive option.
	- A physical server with EC2 instance capacity fully dedicated to your use.
	- Allows you to address compliance requirements and bring your own license.
	- Purchasing options:
		- On-demand.
		- Reserved.
- Dedicated instances:
	- Run on hardware that's dedicated to you.
	- May share hardware with other instances in same account.
	- No control over instance placement.
- Capacity reservations:
	- Reserve on-demand instances capacity in specific AZ for any duration.
	- No time commitment (create/cancel anytime), no billing discounts.
	- Combine with regional reserved instances and saving plans to benefit from billing discounts.
	- You're charged at an on-demand rate whether you run instances or not.
	- For short-term, uninterrupted workloads that need to be in a specific AZ.
# Instance storage
![[Pasted image 20240311230449.png]]
## Elastic block store (EBS) volume
- EBS is a network drive you can attach to your instance while they run.
- It can only mount to 1 instance at a time (at the CCP level).
- They are bound to a specific availability zone, example an EBS volume in us-east-1a can't be attached to us-east-1b. To move volume across, you need to snapshot it.
- It's recommended to detach volume when doing snapshot, because the snapshot only capture data that has been written to EBS volume at the time the snapshot command is issued.
### Benefits
- Data availability.
- Data persistence.
- Data encryption.
- Snapshots.
- Flexibility: live config changes while in production, you can modify volume type, volume size, and IOPS capacity without service interruptions.
### Snapshot features
- The first snapshot is always a full snapshot. It includes all of the data blocks written to the volume at the time of creating the snapshot. Subsequent snapshots of the same volume are incremental snapshots. They include only changed and new data blocks written to the volume since the last snapshot was created.
- The size of a full snapshot is determined by the size of the data being backed up.
- Snapshot lock: protect against accidental or malicious deletions, or to store them in WORM (write-once-read-many).
- Only access in a region.
- Archive:
	- 75% cheaper.
	- Takes within 24 to 72 hours for restoring the archive.
- Recycle bin:
	- Setup rules to retain deleted snapshots for recovery of accidental deletion.
	- Retention period, 1 day to 1 year.
- Fast snapshot restore:
	- Force full initialization of snapshot to have no latency on the first use (expensive).
> [!warning] Important
> AWS does not automatically back up the data stored on your EBS volumes. For data resiliency and disaster recovery, it is your responsibility to create EBS snapshots on a regular basis, or to set up automatic snapshot creation by using AWS Data Lifecycle Manager or AWS Backup.
### Types
- `gp2/gp3 (SSD)`
	- General purpose SSD.
	- Cost-effective storage, low-latency.
- `io1/io2 (SSD)`
	- Highest performance SSD.
	- Critical business applications with sustained provisioned IOPS (IOPS) performance.
	- Support EBS Multi-attach, up to 16 EC2 instance at a time.
- `st1 (HDD)` low cost HDD for frequently accessed, throughput-intensive workloads.
- `sc1 (HDD)` Cold HDD, the lowest cost HDD for less frequently accessed workloads.
### EC2 EBS-backed instances
- The root volume automatically have an EBS volume attached.
- An Amazon EBS-backed instance can be stopped and later restarted without affecting data stored in the attached volumes.
- You can't use `st1` or `sc1` volumes as root volumes.
  ![[Pasted image 20240311234111.png]]
## EC2 instance store
- High performance hardware, lose their storage if they're stopped (ephemeral).
## Elastic File System (EFS)
- Managed network file system (NFS) that can be mounted on many EC2.
- EFS works with EC2 instances with multi-AZ.
- Highly available, scalable, expensive, pay per use.
- Use for content management, web serving, data sharing, ...
- Uses NFSv4.1 protocol.
- Compatible with Linux based AMI (POSIX).
> [!info] Note
> EFS is not supported on Windows instances.
- Scales automatically, pay-per-use, no capacity planning.
### Types
- Regional
- One Zone
### Scale
- 1000s of concurrent NFS clients, 10 GB+ /s throughput.
- Grow to Petabyte-scale network file system, automatically.
### Performance mode
- Set at creation time.
- General purpose (default): latency-sensitive.
- Max I/O: higher latency, throughput, highly parallel.
### Throughput mode
- Brusting.
- Provisioned: set your throughput regardless of storage size.
- Elastic: auto-scale based on your workloads.
### Storage classes
- Standard.
- Infrequent access (EFS-IA): lower cost, should use with one zone, enable with a lifecycle policy.
- Archive.
# Amazon Machine Image (AMI)
- AMI are a customization of an EC2 instance:
	- You add your own software, configuration, operating system, ...
	- Faster boot / configuration time because all your software is pre-packaged.
- AMI are build for a specific region.
- AMIs that are backed by EFS snapshot can take advantage of EBS encryption
- Launch EC2 instances from:
	- Public AMI.
	- Your own AMI.
	- AWS marketplace AMI.
# Elastic Load Balancer (ELB)
- It is a managed load balancer.
- Health checks.
### Why use a load balancer?
- Spread load across multiple downstream instances.
- Expose a single point of access (DNS) to your application.
- Seamlessly handle failures of downstream instances.
- Do regular health checks to your instances.
- Provide SSL termination (HTTPS) for your website.
- Enforce stickiness with cookies.
- High availability across zones.
- Separate public traffic from private traffic.
## Types
- **Classic** - CLB (v1 - old generation - 2009): HTTP, HTTPS, TCP, SSL.
- **Application** - ALB (v2 - new generation - 2016): HTTP, HTTPS, web socket.
	- Routing tables to different target groups based on:
		- Path URL.
		- Hostname URL.
		- Query string, headers.
	- Great for micro services and container-based application.
	- Has a port mapping feature to redirect to a dynamic port in ECS.
- **Network** - NLB (v2 - new generation - 2017): TCP, TLS, UDP.
	- Layer 4:
		- Forward TCP and UDP traffic to your instances.
		- Handle millions of request per seconds.
		- Less latency ~ 100 ms (vs 400 ms for ALB).
	- Has 1 static IP per AZ, and supports assigning Elastic IP.
	- Used for extreme performance.
- **Gateway** - GWLB (2020): Operates at layer 3 (Network layer) - IP Protocol.
	- Deploy, scale, and manage a fleet of third party network virtual appliances in AWS.
	- Example: Firewalls, IPS, IDS, ...
	- Combines the following functions:
		- Transparent network gateway.
		- Load balancer.
	- Uses the GENEVE protocol on port 6081.
- Some load balancers can be setup as internal (private) or external (public) ELBs.
## Sticky Sessions (Session Affinity)
- The same client is always redirected to the same instance behind load balancer.
- Work CLB, ALB, NLB.
- CLB and ALB has expiration date you control.
- Enabling stickiness may bring imbalance to the load over the backend EC2 instances.
### Cookie names
- Application-based cookies:
	- Custom cookie:
		- Generated b the target.
		- Can include any custom attributes required by the application.
		- Cookie name must be specified individually for each target group.
		- Don't use `AWSALB, AWSALBAPP, AWSALBTG`.
	- Application cookie:
		- Generated by the load balancer.
		- Cookie name is `AWSALBAPP`
- Duration-based cookies:
	- Generated by the load balancer.
	- Cookie name is `AWSALB, AWSELB`
### Cross-Zone Load Balancing
- ALB:
	- Enabled by default.
	- No charges for inter AZ data.
- NLB and GWLB:
	- Disable by default.
	- Charges for inter AZ.
- CLB:
	- Disabled by default.
	- No charges for inter AZ data.
  ![[Pasted image 20240310222821.png]]
## SSL/TLS
- The load balancer uses an X.509 certificate.
- You can manage certificates using AWS Certificate Manager (ACM).
### Server Name Indication (SNI)
- SNI solves the problem of loading multiple SSL certificates onto the web server (to serve multiple websites).
- It's a "newer" protocol, and requires the client to indicate the hostname of the target server in the initial SSL handshake.
- The server will then find the correct certificate, or return the default one.
## Connection draining
- Feature naming:
	- Connection draining for CLB.
	- Deregistration delay for ALB and NLB.
- Time to complete "in-flight requests" while the instance is de-registering or unhealthy.
- Stops sending new requests to the EC2 instance, which is de-registering.
# Auto Scaling Group (ASG)
- Scale out / in to match the workload.
- Ensure minimum and maximum number of EC2 instances running.
- Auto register new instances to a load balancer.
- Re-create an EC2 instance in case a previous one is terminated.
- ASG is free.
## Auto Scaling
- Can use CloudWatch to monitor and scale based on the alarms.
- Predictive scaling: continuously forecast load and schedule scaling ahead.
### Policies:
- Target tracking
	- Example: I want the average ASG CPU to stay at around 40%.
- Simple / Step: based on metric.
- Scheduled actions.
### Scaling cooldowns
- After a scaling activity happens, a cooldown period will occur. In this time, ASG will not launch or terminate additional instances.
- Use ready-to-use AMI to reduce configuration time in order to be serving request faster and reduce the cooldown period.
### Instance refresh
- In order to update launch template, then re-creating all EC2 instances:
	1. A setting of minimum healthy percentage is set.
	2. Replace instances with old template.
# Instance metadata (IMDS)
- Retrieve metadata of the EC2 instance, but not the IAM Policy.
- IMDSv1 can be accessed directly through `http://169.254.196.254/lastest/metadata`.
- IMDSv2 is more secure:
	1. Get session token using headers and PUT
	2. Use session token in IMDSv2 calls using headers `X-aws-ec2-metadata-token: $TOKEN`
# Questions
- For compliance purposes, you would like to expose a fixed static IP address to your end-users so that they can write firewall rules that will be stable and approved by regulators. What type of Elastic Load Balancer would you choose?
	- NLB
	- Network Load Balancer has one static IP address per AZ and you can attach an Elastic IP address to it. Application Load Balancers and Classic Load Balancers as a static DNS name.