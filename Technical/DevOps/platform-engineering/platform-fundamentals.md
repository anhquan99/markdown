# Platform Fundamentals
## Cattle vs pets
- Metaphor for the on premise and serverless. In this case the pets are on premise servers and the cattle is serverless resources (ie: AWS, Azure, ...).
- _Pets_ to _Cattle_ is essential for modern cloud computing, scalability, and security. It eliminates human error, prevents malicious actors from maintaining a permanent foothold, and allows systems to scale up or down automatically.

## Dora metric
### Deployment frequency
- Elite: Multiple deployments per day
- High: deploy daily to weekly
- Medium: once per week and once per month
- Low: once per month and once every six months
### Lead to for changes
- Duration from code commit to successful production release
- Elite: less than 1 hour
- High: 1 day to 1 week
- Medium: a week to a month
- Low: 1 to 6 months
### Change failure rate
- Percentage if deployments that cause service degradation requiring a hotfix, rollback or patch.
- Elite: 15%
- High: 30%
- Medium: 45%
- Low: 60%
- Quality balance: high deployment frequency must be balanced with acceptable failure rate.
#### Strategies to reduce change failure rates
- Comprehensive testing
- Policy as code
- Progressive deployment
- Environment parity: consistent configurations across environments
### Mean time to recovery
- Elite: < 1 hour
- High: between 1 hour and 1 day
- Medium: between 1 day and 1 week
- Low: between 1 week and 1 month
## Platform maturity models
### Maturity progression
- Level 1: provisional
- Level 2: operational
- Level 3: Scalable
- Level 4: Optimizing
## Runbook
- A runbook is a detailed “how-to” guide for completing a commonly repeated task or procedure within a company’s IT operations process (e.g. provisioning, software updates / deployment, change configs, and opening ports).
- Runbooks are created to provide everyone on the team—new or experienced—the knowledge and steps to quickly and accurately resolve a given issue. For example, a runbook may outline routine operations tasks such as patching a server or renewing a website’s SSL certificate.
- Runbooks are extremely helpful for incident response operations.
- A playbook deals with the overarching responses to larger issues and events, and can include multiple runbooks and team members as part of the complete workflow.
## Trunk based development
- **Trunk-Based Development (TBD)** is a version control management practice where developers merge code changes into a single central branch—the "trunk" or "mainline"—frequently, avoiding long-lived feature branches.
  ![](/image/Pasted%20image%2020260615204125.png)
- Developers commit code to a single central branch (the "trunk") multiple times a day or via very short-lived feature branches.
### Compare to gitflow
- Gitflow relies on strict, parallel branching. Developers write code on `feature` branches, which eventually merge into a `develop` branch. From there, code moves to `release` branches for testing, and finally to `main` for production.
  ![](/image/Pasted%20image%2020260615204534.png)
## K8s scaling
- Horizontal Pod Autoscaler (HPA): add or remove pod replicas.
- Vertical Pod Autoscaler (VPA): resizes existing pods by adjusting their CPU and memory requests or limit.
- Node autoscaling: automatically adjusts the number of worker nodes in your cluster to match application demand and control infrastructure costs.
	- Products: `karpenter`
	- Scale to zero products: `keda` - event driven autoscaling
## Dynamic Resource Allocation
- A Kubernetes feature that changes how workloads request and assign specialized hardware, like GPUs, FPGAs, and AI accelerators.
- Think of it like **Dynamic Volume Provisioning**, but instead of claiming _storage_, pods use it to claim _hardware capabilities_.
## Progressive Disclosure
- A UX design pattern that reduces cognitive load by gradually revealing more complex information or features as the user progresses through the UI of a digital product (such as a Internal Developer Portal).
## Golden paths
- A golden path is a preconfigured, paved road that provides an end-to-end workflow for developers, enabled via an Internal Developer Platform (IDP).
- It's a predefined route that guides developers through common tasks, designed to reduce cognitive load and ensure that they can operate safely and in compliance.
- A crucial aspect of a golden path is its focus on self-service capabilities. This means that developers can autonomously use the platform to perform tasks without the need for constant interaction with operations or infrastructure teams.
### Advantages
- Reduced cognitive load
- Improved consistency and reliability
- Faster development cycles:
- Enhanced security and compliance
- Increased developer satisfaction