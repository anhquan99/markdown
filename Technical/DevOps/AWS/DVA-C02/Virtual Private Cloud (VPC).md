- VPC is a private network to deploy your resources (regional resource).
- Subnets allows you to partition your network inside your VPC.
- To define access to the internet and between subnets, use Route Tables.
# Gateway
- Internet Gateways helps VPC instances connect with the internet.
- Public subnets have a route to the internet gateway.
- Network Address Translation (NAT) Gateways (AWS managed) and NAT Instances (self-managed) allows instances in private subnets to access the internet while remaining private.
# Subnets
- A range of IP addresses in your VPC. Each subnet must reside entirely within one Availability Zone and can not span zones. By launching AWS resources in a separate Availability Zone, you can protect your applications from the failures of a single Availability Zone.
## Subnet IP address range
- IPv4 only.
- Dual stack: has both IPv4 and IPv6.
- IPv6 only.
## Types
- Public subnet: the subnet has direct route to an internet gateway, resources in a public subnet can access the public internet.
- Private subnet: has no direct route to an internet gateway, resources in a private subnet require a NAT device to access the public internet.
- VPN-only subnet: has a route to a Site-to-Site VPN connection through a virtual private gateway and does not have a route to an internet gateway.
- Isolated subnet: has no routes to destinations outside its VPC. Resources in an isolated subnet can only access or be accessed by other resources in the same VPC.
## Subnet CIDR reservations
- A range of IPv4 and IPv6 addresses that you set aside so that AWS can't assign them to your network interfaces.
- Options:
	- Prefix: AWS assigns address from the reserved IP address range to network interfaces.
	- Explicit: manual.
# Network Access Control List (ACL)
- A firewall controls traffic from and to a subnet.
- Only allow, deny rules and attached at the subnet level.
- Only include IP.
# Security groups
- A firewall controls traffic to and from an Elastic Network Interface (ENI) / an EC2 instance.
- Only allow rules, include IP and other security groups
# NAT gateways
- Enables instances in a private subnet to send outbound traffic to the internet, but prevents resources on the internet from connecting to the instances.
## Use cases
### Access the internet from a private subnet
- In Availability Zone B, the public subnet contains a NAT gateway, and the instances in the private subnet can reach the internet through a route to the NAT gateway in the public subnet. 
- Both private and public NAT gateways map the source private IPv4 address of the instances to the private IPv4 address of the private NAT gateway, but in the case of a public NAT gateway, the internet gateway then maps the private IPv4 address of the public NAT Gateway to the Elastic IP address associated with the NAT Gateway. When sending response traffic to the instances, whether it's a public or private NAT gateway, the NAT gateway translates the address back to the original source IP address.
  ![[Pasted image 20240304210229.png]]
### Access your network using allow-listed IP address
- Traffic from the instances is routed to a virtual private gateway, over the VPN connection, to the customer gateway, and then to the destination in the on-premises network. If the destination allows traffic only from a specific IP address range, it will prevent traffic from these instances from reaching the on-premises network.
  ![[Pasted image 20240304211341.png]]
  - By using NAT gateway before being routed to the VPN connection, the on-premises network receives the traffic from the instances with the source IP address of the NAT gateway, which is from the allowed IP address range.
    ![[Pasted image 20240304213133.png]]
### Enable communication between overlapping networks
- Traffic from an instance in the non-routable subnet of VPC A that is destined for the instances in the non-routable subnet of VPC B is sent through the private NAT gateway and then routed to the transit gateway. The transit gateway sends the traffic to the Application Load Balancer, which routes the traffic to one of the target instances in the non-routable subnet of VPC B. The traffic from the transit gateway to the Application Load Balancer has the source IP address of the private NAT gateway. Therefore, response traffic from the load balancer uses the address of the private NAT gateway as its destination. The response traffic is sent to the transit gateway and then routed to the private NAT gateway, which translates the destination to the instance in the non-routable subnet of VPC A.
  ![[Pasted image 20240304213356.png]]

| Security group                                                                                                                                                | Network ACL                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Stateful: return traffic is automatically allowed, regardless of any rules                                                                                    | Stateless: return traffic must be explicitly allowed by rules                                                                                |
| Evaluate all rules before deciding whether to allow traffic                                                                                                   | Process rules in number order when deciding whether to allow traffic                                                                         |
| Applies to an instance only if someone specifies the security group when launching the instance, or associates the security groups with the instance later on | Auto applies to all instances in the subnets it's associated with (therefore, you don't have to rely on users to specify the security group) |
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
```ad-note
- S3 and DynamoDB are the only 2 services that have a Gateway Endpoint available. All the other services have an interface endpoint (powered by Private Link).
```