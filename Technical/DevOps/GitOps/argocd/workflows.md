# Workflows
- It is a container-native workflow engine for orchestrating parallel jobs on k8s.
## Support
- Define workflows where each step is a container
- Model multistep workflows as a sequence of tasks or capture the dependencies between tasks using a directed acyclic graph (DAG).
- Easily run compute intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes. 
## Use cases
- Machine learning pipelines
- Data and batch processing
- Infrastructure automation
- CI/CD
- ...
## Architecture
![](Image/Pasted%20image%2020260410222447.png)
- Argo Server
- Workflow Controller
- User namespace: act as execution plane
### Workflow controller
![](/Image/Pasted%20image%2020260410223408.png)
## Specification
### Workflow
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow 
metadata: 
	generateName: hello-world- # Name of this Workflow
spec:
	entrypoint: hello-world # Defines "hello-world" as the "main" template
	templates: 
	- name: hello-world # Defines the "hello-world" template 
	  container: 
		  image: busybox 
		  command: [echo] 
		  args: ["hello world"] # This template runs "echo" in the "busybox" image with arguments "hello world"
```
