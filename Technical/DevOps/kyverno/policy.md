# Policy
## Definition
- Policy is the heart of Kyverno.
- A policy is a collection of rules to tell k8s what we want to enforce in our cluster.
## Structure
![](/image/Pasted%20image%2020260604093728.png)
- Match: define target resources.
- Exclude: define resources to exclude or exception.
- Rule: define what to do
	- Validate: check if request is allowed
	- Mutate: modify request
	- Generate: create new resource
	- Verify images: check the image signature
## Types
- Policy: a name space resource.
- ClusterPolicy: a cluster-wide resource.