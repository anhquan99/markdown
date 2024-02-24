## Cloud
 - Public cloud: open for everyone to purchase over public internet, all the resources of the cloud are owned and maintain by the cloud provider.
- Private cloud: services and resources are available exclusively for one organization, often for strict security controls and isolation are required. Hardware, software, maintenance, updates and security are mananged by the organization and all the cost is paid by that organization.
- Hybird cloud: compose public and private cloud models, taking advantage of the benifits of each.
## Cloud service models
1. Infrastructure as Service (Iaas):
	-  Rent a whole virtual machine, you don't need to maintain the hardware or underlying hardware.
	- You pay for your resource (Operation Expenditure - OpEx).
2. Platform as a Service (PaaS):
	- Pay for a hosting service, the cloud all the underlying hardware, OS and more.
	- The consumption-base model makes this a cost-effective model.
3. Software as a Service (SaaS):
	- Cloud provider provides the software, you need to manage the user who can use it.
	- Ex: Office 365, ...
## Regions and availability
- A region is a geographical are within which one or more (usually more) Azure data centers reside, connected to each other with low-latency network (less than 2 milliseconds). Availability zones consist of one or more physically datacenter to back up if one or others go down, the minimum three zones within a single region.
## Azure resource manager (ARM)
- Resource groups are units of management for your logically related resources. With ARM, all resources must be a member of a resource group, and only one, although supported resources can be moved between resource groups.
- ARM templates is a JSON file. You define what resources you want to deploy and any dependencies, and ARM will handle the orchestration. ARM allow you to deploy resources, defined in code - Infrastructure as Code (IaC) solutions.
- A benefit of ARM is idempotence, in which you can deploy a resource that already had been deployed but if some resources are missing, the ARM will deploy those missing resources.