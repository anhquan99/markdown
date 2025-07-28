- AKS is a fully managed [[Technical/DevOps/kubernetes/kubernetes|kubernetes]] cluster.
![[Pasted image 20240904132737.png]]
# Features
- **Control plane**: installation and management for free
- **Upgrades**
- **Patching**: automated security patching
- **Self healing**: control plane auto health management
- **Networking**
- **Scaling**
- **Encryption AD RBAC**
# Networking
## Basic
- By default, AKS clusters use `kubenet`, and this will create an Azure virtual network and subnet for you.
- Using `kubenet`, only the nodes receive an IP address in the virtual network subnet and pods can't communicate directly with each other. Instead, User Defined Routing (UDR) and IP forwarding are used for connectivity between pods across nodes.
- In basic networking pods, IP natted inside subnet. NAT is used so that the pods can reach resources on the Azure virtual network.
![[Pasted image 20240904133753.png]]
## Advanced networking
- Use Azure Container Networking Interface (CNI) every pod gets an IP address from the subnet and can be accessed directly via their private IP address from connected network.
- These IP addresses must be unique across your network space and must be planned in advance.
- Advance networking requires more planning if all IP addresses used then we need to rebuild clusters in a larger subnet as your application demands.
![[Pasted image 20240904134040.png]]
# Storage
- **Azure disk**: used to create a k8s `DataDisk` resource and mounted as `ReadWrite`, so the disk are only available to a single pod.
- **Azure files**: SMB based shared file system mounted across multiple nodes and pods.
# Security
![[Pasted image 20240904135549.png]]