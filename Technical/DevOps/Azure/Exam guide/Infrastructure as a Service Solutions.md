# Provision VMs in Azure
- Considerations when provisioning VMs:
	- Availability
		- You should avoid running a production workload on a single VM, it wouldn't be resilient in the face of planned or unplanned maintenance.
		- Fault domains: logical groups of hardware within a data center that share the same power and network hardware. Limitation is the impact of physical hardware failures and power interruptions.
		- Update domains: are logical groups of hardware within a data center that might undergo maintenance or be rebooted at the same time. Only 1 update domain is rebooted at any 1 time and given 30 minutes to recover before maintenance is initiated across multiple update domains to increase availability.
		- Proximity placement groups: ensure that they are physically located close to each other for when you have low latency requirements.
		- Infrastructure redundancy selections:
			- Availability zones
			- Virtual machine scale sets: distributing VMs across multiple availability zones and fault domains.
			- Availability sets:  are logical groups of VMs within a data center, where VMs are distributed across fault domains and update domains. Availability sets themselves don't carry any costs, you only need to pay the VM instances that you create.
	- Disks
		- Disk type:
			- Standard: cost effective for development and testing.
			- Premium: for production.
		- Disk storage:
			- Managed disk: managed by Azure.
			- Unmanaged disk: managed by yourself.
	- Limits: CPUs limits
	- Location
	- Naming: naming conventions for VMs.
	- Operating system image
	- Post-provision configuration
	- Size
	- Pricing model
# ARM templates
```
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {}
}
```
- Structure:
	- `$schema` (required): describes the version of the template language to be used. The `deploymentTemplate` represents the resource group being used.
	- `contentVersion` (required): version of your templates.
	- `parameters`: resolve before starting the deployment operations. Limited to 256 parameters in a template.
	- `functions`: define a custom functions to help control or naming in ARM.
	- `variables`: unlike parameters, variables don't need their type defined, as it will be inferred from the value of the variable, like functions, but they resolve values and that value is immutable once resolved. Variables don't have their value defined by input at deployment time.
	- `resources` (required): define the resources you want to deploy or update as part of the template deployment. The required resource elements are:
		- `type`
		- `apiVersion`
		- `name`
		- `location`
		- If the child resource is defined separately from the parent, you will need to add the parent's type with a `/`, followed by the child's type. Example:
	```
	{
	
	  "type": "**Microsoft.Sql/servers**",
	  "apiVersion": "2021-02-01-preview",
	  "name": "**parentsqlsvr**",
	  "location": "North Europe",
	  "properties": {}
	},
	{
	  "type": "**Microsoft.Sql/servers/databases**",
	  "apiVersion": "2021-02-01-preview",
	  "name": "**parentsqlsvr/childsqldb**",
	  "location": "North Europe",
	  "properties": {}
	}
	```
	- `outputs`: return values from deployed resources.
- Deploying multiple resources
	- Multi-tiered templates: declare an ARM file with multiple resources in 1 file.
	- Nested templates: declare resources with multiple files.
	- Parameter file: used to define value for parameters.
# ARC tasks
- Quick tasks:
	- Having your image built, tagged, and pushed from within the cloud.
	- Run your image in a temporary container within ACR itself.
- Automatically triggered tasks:
	- Source code update.
	- Base image update.
	- Schedule.
- Multi-step tasks: perform multiple `build` and `push` tasks in series or parallel.
# Azure container instances (ACI)
- ACI has the concept of container group, within which multiple containers share a life cycle, resources, network, ... because they will be running on the same host, the same concept of Pod of K8s.
- Considerations:
	- Container images can't be larger than 15 GB.
	- If a container group restarts, the container group's IP may change. Use hard coded IP address in this scenario.
	- There are ports that are reserved for service functionality, you should not use these ports because their use will lead to unexpected behavior.
	- Your container groups may restart due to platform maintenance events.
	- ACI doesn't allow privileged container operations.
- A container group is a collection of containers that get scheduled on the same host machine. The containers in a container group share a lifecycle, resources, local network, and storage volumes.
- Liveness probes: containerized applications may run for extended periods of time, resulting in broken states that may need to be repaired by restarting the container. The feature supports you to configure your containers within your container group to restart if critical functionality is not working.
- Readiness probes: configure your container can't be accessed under certain conditions. For example, a container application might need to load a large data set during startup, and you don't want it to receive requests during this time.
- Storage
	- Azure file share
		- Created with Azure files, hosted in Azure Storage.
		- Limitations:
			- Only available on Linux containers.
			- Volume mount requires as `root`
			- Volume mount are limited to CIFS support.
	- Secret volume
		- Use a secret volume to supply sensitive information to the containers in a container group. The secret volume stores you secret in files within the volume, accessible by the containers in the container group.
		- Once deployed with secrets in a container group, a secret volume is read-only.
		- All secret volumes are backed by `tmpfs`, a RAM-backed filesystem; their contents are never written to non-volatile storage.
	- emptyDir
		- The `emptyDir` volume provides a writeable directory accessible to each container in a container group. Containers in the group can read and write the same files in the volume, and it can be mounted using the same or different paths in each container.
		- Use cases:
			- Scratch space
			- Checkpointing during long-running tasks
			- Store data retrieved by a sidecar container and served by an application container
		- Data in an emptyDir volume is persisted through container crashes. Containers that are restarted, however, are not guaranteed to persist the data in an emptyDir volume. If you stop a container group, the emptyDir volume is not persisted.
		- Max size is 50GB.
	- gitRepo
		- Mounts a directory and clones the specified Git repo into it at container startup. By using a gitRepo volume in your container instances, you can avoid adding the code for doing so in your application.
- Container orchestrators
	- The task of automating and managing a large number of containers and how they interact is known as orchestration.
	- Azure container instances provides some of the basic scheduling capabilities of orchestration platform.
- Traditional orchestration
	- Scheduling: given a container image and a resource request, find a suitable machine on which to run the container.
	- Affinity/Anti-affinity: specify that a set of containers should run nearby each other (for performance) or sufficiently far apart (far availability).
	- Health monitoring: watch for container failures and automatically reschedule them.
	- Failover: keep track of what is running on each machine, and reschedule containers from failed machine to healthy nodes.
	- Scaling: add or remove container instances to match demand, either manually or automatically.
	- Networking: provide an overlay network for coordinating containers to communicate across multiple host machine.
	- Service discovery: enable containers to locate each other automatically, even as they move between host machines and change IP addresses.
	- Coordinated application upgrades: manage container updates to avoid application downtime and enable rollback if something goes wrong.
- Virtual networking
	- Scenarios
		- Direct communication between container groups in the same subnet.
		- Send task-based workload output from container instances to a database in the virtual network.
		- Retrieve content for container instances from a service endpoint in the virtual network.
		- Enable container communication with on-premises resources through a VPN gateway.
		- Integrate with Azure Firewall to identify outbound traffic originating from the container.
		- Resolve names via the internal Azure DNS for communication with Azure resources in the virtual network, such as virtual machines.
		- Use NSG rules to control container access to subnets or other network resources.
	- Unsupported networking
		- Azure Load Balancer: placing an Azure Load Balancer in front of container instances.
		- Global virtual network peering: connecting virtual networks across Azure regions.
		- Public IP or DNS label: Container groups deployed to a virtual network don't currently support exposing container directly to the internet with a public IP address or FQDN. 
		- Managed Identity with Virtual Network in Azure Government Regions.
	- Virtual network: defines the address space in which you create one or more subnet. You then deploy Azure resources (like container groups) into the subnets in your virtual network.
	- Subnet (delegated): subnets segment the virtual network into separate address spaces usable by the Azure resources you place in them. Once delegated, the subnet can be used only for container groups. If you attempt to deploy resources other than container groups to a delegated subnet, the operation fails.
	- Network profile: a network profile is a network configuration template for Azure resources. It specifies certain network properties for the resource, for example, the subnet into which it should be deployed.
- Confidential containers
	- Enable customer to run Linux containers within a hardware-based and attested Trusted Execution Environment (TEE).
# Questions
- Which element can you add to an ARM template to define **apiVersion** for all resources of a specific type, so that you don’t have to specify **apiVersion** for each resource?
	- `apiVersions`
- We know we can run Linux containers on a Windows machine; can we also run Windows containers on a Linux machine?
	- No. With Windows, Docker run as a VM and use linux kernel. But on Linux, Docker does not use the Windows kernel, so Windows containers can not run on Linux.
## Notes
- Command for deleting image from ACR `az acr repository delete --name {registry-name} --image {image-name:tag}`