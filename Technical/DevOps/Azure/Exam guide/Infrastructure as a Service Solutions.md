# Provision VMs in Azure
- Considerations when provisioning VMs:
	- Availability
		- You should avoid running a production workload on a single VM, it wouldn't be resilient in the face of planned or unplanned maintenance.
		- Fault domains: logical groups of hardware within a data center that share the same power and network hardware. Limitation is the impact of physical hardware failures and power interruptions.
		- Update domains: are logical groups of hardware within a data center that might undergo maintenance or be rebooted at the same time. Only 1 update domain is rebooted at any 1 time and given 30 minutes to recover before maintenance is initiated across multiple update domains to increase availability.
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
	- `parameters`
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
# Questions
- Which element can you add to an ARM template to define **apiVersion** for all resources of a specific type, so that you don’t have to specify **apiVersion** for each resource?
	- `apiVersions`
- We know we can run Linux containers on a Windows machine; can we also run Windows containers on a Linux machine?
	- No. With Windows, Docker run as a VM and use linux kernel. But on Linux, Docker does not use the Windows kernel, so Windows containers can not run on Linux.