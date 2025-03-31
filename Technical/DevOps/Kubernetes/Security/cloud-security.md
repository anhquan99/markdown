# Cloud Security
## Definition for computer security
- Computer security is the protection of item of value, called assets, of a computer or computer system.
- Security process:
	- **Integrate into the Assets Life Cycle**: it is important that there is a chain of custody for the physical assets, up to and including when the asset is powered on.
	- **Integrate into the Software Development Life Cycle**: it is critical that security is considered during the design phase of a project.
	- **Establishing Organizational Policies and Procedures**: reduces the risks of accidental or intentional harm.
	- **Defining Roles and Responsibilities**: it is important to establish a hierarchical structure for incident response. There needs to be a defined leader or plan for assigning a leader due to the strenuous nature of the job.
## Basic principles
### Assessment
- The operation of determining the value of assets and the cost of implementing security to protect those assets, generally on the most valuable assets and focus on the threats that affect the assets.
### Prevention
- Known risks is the easiest and likely the most cost-effective principle to incorporate into a system.
- Prevention is the implementation of security measures, called controls, to protect assets identified during the assessment stage. Controls refer to software and strategies deployed to protect the assets.
- For each asset, there is some aspect that needs to be protected.
- There are an unlimited number of threats out in the world; they can be accidental, malicious, and directed. It is impossible to prevent them all, which is why the assessment stage is important.
#### Control types
- Technical controls involve software and hardware to protect assets.
- Procedural controls involve processes and policies designed to prevent loss and damage,
- Physical controls involve facilities, staff, key cards, locks, and other measures.
### Detection
- Involves monitoring through the use of various technologies.
- Detection can be the most expensive and can be difficult to execute effectively.
- Intrusion Detection and Prevention Systems (IDPS) are used to identify possible incidents, create a consistent audit trail, and report attempted intrusions.
- Incident detection methods include:
	- Signature-based
	- Statistical anomaly-based: creating baseline and monitoring for anomalies. 
	- Stateful protocol analysis
### Reaction
- The least considered principles, and catastrophes often result from poorly planned reactions to vulnerabilities and risks.
- How an incident is addressed may have long term effects and must be considered carefully. While detection of system risks is critically important, how an organization reacts to these vulnerabilities can determine the survival of the system and, in some cases, the organization itself.
- It is important to maintain a focus on problem solving. Work on solutions rather than specific blame. Blame is an unproductive activity. Unexpected failures will occur no matter how much planning and implementation is effected. Root cause analysis is productive for identifying technical issues.
## Attack types
### Active attacks
- The attempts to alter system resources or affect their operation so it compromises Integrity or Availability.
### Passive attacks
- The attempts to learn or make use of information from the system, but does not affect system resources, it compromises Confidentiality.
## 4Cs of Security
- Each layer greatly affects the layers within, one cannot protect everything from the Code layer if there are vulnerabilities at the Cluster level.
- There are many cloud providers, each with their own security best practices and settings. It may be a good idea to research the settings rather than trust.
- Container security relies on trusted code. This layer is a combination of container vulnerabilities scanning, image signing to ensure nothing has been modified, and also preventing the leveraging of elevated privileges past the the least privileges required.
![[image-14.png]]
## Limit access
- A central concept in security is limiting external access to the production environment. There is always a balance between allowing end users the appropriate amount of access without allowing more than the least access possible. This is made up of several layers, with the complexity and number of layers tied to the sensitivity of the information being protected.
- Protecting network traffic is not just an edge decision. Every connection, both between nodes, as well as intra-node communication, should have a series of firewalls working in conjunction with each other.
- A dynamic CI/CD environment may not be as protected due to the necessary dynamic nature of constant change. As a result, it is essential to insert scanning and verification tools as part of the pipeline, as well as ongoing reviews and assessment to ensure problems are properly caught and fixed.
- There should also be a collection of non-container specific security tools (e.g., SELinux, Kerberos, SAML, etc.) to manage access at each layer, from hardware up to the application.