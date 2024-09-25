- A CI/CD tool.
# Usage 
- CI for application and infrastructure code.
- CD pipelines to deploy application to different environments using Jenkins pipeline as code.
- Infrastructure component deployment and management using IaC tools.
- Run batch operations using Jenkins jobs.
- Run ad-hoc operations like backup, cleanup, remote script execution, event triggers, etc, ...
# Architecture
![[Pasted image 20240910204851.png]]
## Jenkins server
- A control server that holds all key configurations and orchestrates all the work defined in the pipelines.
# Jenkins jobs
- A job is a collection of steps that you can use to build your source code, test your code, run a shell script, ...
- Can be called Jenkins pipeline.
## Jenkins plugins
- Stored in folder `/var/lib/jenkins`
## Jenkins global security
- Authentication methods:
	- Jenkins's own user database
	- LDAP integration
	- SAML Single Sign On
## Jenkins credentials
- Types:
	- Secure text
	- Username & password
	- SSH keys
- Stored in folder `$JENKINS_HOME/secrets/`
## Jenkins agent
- Agents are the worker nodes that execute all the step in a job.
- Every agent has a label as a unique identifier.
### Types
- Agent nodes: static agents which is up and running all the time and stay connected to the Jenkins server.
- Agent clouds: dynamic agent. Whenever you trigger a job, an agent gets deployed on demand and get deleted once the job is completed. 
# General terms
- Artifact: An immutable file generated during a Build or Pipeline run which is **archived** onto the Jenkins Controller for later retrieval by users.
- Core: The primary Jenkins applications (`jenkins.war`) which provides the basic web UI, configuration, and foundation upon which Plugins can be built.
- 