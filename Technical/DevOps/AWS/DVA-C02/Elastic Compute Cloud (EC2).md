- Infrastructure as a Service.
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
## Elastic block store (EBS) volume
- EBS is a network drive you can attach to your instance while they run.
- It can only mount to 1 instance at a time (at the CCP level).
- They are bound to a specific availability zone, example an EBS volume in us-east-1a can't be attached to us-east-1b. To move volume across, you need to snapshot it.
- It's recommended to detach volume when doing snapshot, because the snapshot only capture data that has been written to EBS volume at the time the snapshot command is issued.
### Snapshot features
- Archive:
	- 75% cheaper.
	- Takes within 24 to 72 hours for restoring the archive.
- Recycle bin:
	- Setup rules to retain deleted snapshots for recovery of accidental deletion.
	- Retention period, 1 day to 1 year.
- Fast snapshot restore:
	- Force full initialization of snapshot to have no latency on the first use (expensive).