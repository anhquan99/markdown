- VPC is a private network to deploy your resources (regional resource).
- Subnets allows you to partition your network inside your VPC.
- To define access to the internet and between subnets, use Route Tables.
# Gateway
- Internet Gateways helps VPC instances connect with the internet.
- Public subnets have a route to the internet gateway.
- Network Address Translation (NAT) Gateways (AWS managed) and NAT Instances (self-managed) allows instances in private subnets to access the internet while remaining private.
# Network Access Control List (ACL)
- A firewall controls traffic from and to a subnet.
- Only allow, deny rules and attached at the subnet level.
- Only include IP.
# Security groups
- A firewall controls traffic to and from an Elastic Network Interface (ENI) / an EC2 instance.
- Only allow rules, include IP and other security groups.

| Security group | Network ACL |
| --- | --- |
| Stateful: return traffic is automatically allowed, regardless of any rules | Stateless: return traffic must be explicitly allowed by rules |
| Evaluate all rules before deciding whether to allow traffic | Process rules in number order when deciding whether to allow traffic |
| Applies to an instance only if someone specifies the security group when launching the instance, or associates the security groups with the instance later on | Auto applies to all instances in the subnets it's associated with (therefore, you don't have to rely on users to specify the security group)|
# Flow logs
- Capture information about IP traffic going into your interfaces:
	- VPC flow logs data can go to S3, CW logs and Kinesis Data Firehose
	- Subnet flow logs
	- ENI flow logs
- Monitor and troubleshoot connectivity issue.
# VPC peering
- Connect 2 VPC, privately using AWS network.
- Must not have overlapping CIDR.
- VPC peering connection is not transitive. If A connect B and B connect C, it does not mean A connect C and if A want to connect C then A must set up peering to C.
# VPC endpoints
- Allows VPC connects to AWS services using a private network instead of the public network.
# VPN
## Site to site VPN
- Connect to an on-premises VPN to AWS.
- Goes over the public intenet.
## Direct connect (DX)
- Establish a physical connection between on-premises and AWS.
- Goes over a private network.
## Notes
- S3 and DynamoDB are the only 2 services that have a Gateway Endpoint available. All the other services have an interface endpoint (powered by Private Link).