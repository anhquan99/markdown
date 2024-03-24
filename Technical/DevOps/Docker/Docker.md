# Definition
- What is [[Technical/DevOps/Docker/Container|container]]? ^d2bd26
	- Container: a package of code where you put code, tools - dependencies to run that code.
	- Like the real life container, it contains the goods. And whenever needed to move those goods around, you just need to take that container move to where you want. You don't get any unexpected when opening the container. The same with container always yields the exact same application and execution behavior. No matter where or by whom, it might be executed.
- What is [[Image]]? ^7a8e6c
	- The templates/blueprints for containers
	- Contain code + required tools/runtimes
	- Image is a snapshot of  you code.
- What is docker?
	- Docker is a container technology for creating and managing containers.
	- Docker supports for containers is built into modern operating systems, and simplifies the creation and management of such containers.
- Why docker?
	- Docker make project independent, standardized "application package". There are some reason for this:
		- Different development and production environment
		- Different development environment within a team/company
		- Clasing tools/versions between different projects.
- Virtual machines (VM) vs docker container:
	- VM problems:
		- The VM create waste of resources in computer because it must install the whole new system in the computer, so it is a computer OS in a computer OS.
		- VM run slower because the whole process is run on an OS.
		- It's hard to share the VM and can be challenging.
	- A docker container solves the problem of VM problems. 
		- A docker container runs on OS built-in container support and a docker engine. 
		- The container is a small running OS but not whole OS itself, there the resources are not going to be wasted and runs faster than VM.
		- Docker help you to create image, it is easy to share the image.
# [[Docker tools and building blocks]]
# [[Technical/DevOps/Docker/Volumes]]
# [[Technical/DevOps/Docker/Network]]
# [[Docker compose]]
# [[Utility container]]
# [[From development to production]]
# [[Note]]
