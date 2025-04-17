# Workload Consideration
## Before downloading image
- The best place to begin security is before software has ever been accessed. Only allowing trusted repositories is the first step. Even then, utilize keys and hashes to make sure the package has not been modified after the original programmer finished their work.
- Using as small a base image as possible to lower the potential complexity and better analyze what issues the microservice could contain.
## Before running a container
- Static Analysis checks issue with code prior to running it.
- Dynamic Analysis checks how software actually runs in a protected, or sandbox environment.
- As the code will be running, and perhaps compromising the system, there is greater risk to dynamic analysis. If the images are from trusted repositories, and static analysis has not found any issues, it is still important to analyze the program as it runs. This task would typically be done on a development or non-essential cluster, not in production.
- Images could be downloaded and analyzed, or scanned, via static and dynamic analysis, then added to the local repository. Another way to scan images would be through the use of a Dynamic Admission Controller. You would first set up a tool like Sysdig or Aqua, and then add a `ValidatingWebhookConfiguration`, which uses a webhook whenever a `CREATE` or `UPDATE` call is made. Be aware that this will add a lot of time to the creation process, as the scan may download the image and scan for vulnerabilities. A 30 second timeout may expire before the scan completes, causing the creation process to fail.
## Tools
### Static analysis
- Docker Bench
- kube-bench
- Clair
- Trivy
### Dynamic analysis
- `perf` and `ftrace` Linux commands
- Tracee
- eBPF
- Falco
## Leverage admission controller
- The optimal situation is when scanning is done prior to the container or software being allowed into the production environment. Dynamic admission controller can be leveraged, every API call to create an image would then reference an outside tool in order to scan and approve an image prior to that image being accepted.
- While this setup is beneficial for checking all images, it also adds a lot of latency and resource usage to the environment. Every time the same, previously-cleared, image is requested again, it will be checked. The amount of latency depends on access to the external source and the size and complexity of the image.
- A common option is to leverage a dynamic admission controller to insert an `initContainer`: containing a scan or verification tool into the pod spec. Then the load is distributed across all the workers. Only if the `initContainer` scans and has a zero exit code, is the rest of the pod spec passed to the container engine for execution.