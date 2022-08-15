# Docker
## What is Docker? 
- Docker is an open source platform for building, deploying and managing containerized application.
- Lightweight: Containers share the machine's OS system kernel and therefore do not require an OS per application, driving higher server efficiencies and reducing server and licensing costs.
- Secure application are safer in containers, and Docker provides the strongest default isolation capabilities in the industry.
- There are windows docker and linux docker.
## Containers and Images
- Image: is a snapshot of a virtual machine.
- Image has layers, when building the image it must base on some base image then user will add more layers to add more feature to the image.
- Container: is an instance of an image running when user start image.
- Docker image is built based on layer, when building image if image has many layers, docker will create some `none` image to cache the layer for the next build.
## Command
- Docker verions: `docker --version`
- Docker info: `docker info`
- Images command:
	- All images: `docker image ls`
	- Image search `docker search ${docker-image-name}`
	- Image filter: `docker image ls --filter "${docker-image-name}"` ???
	- Inspect image: `docker image inspect ${docker-image-id}`
	- Pull image: `docker image pull ${docker-image-name}:${docker-image-version}`, default version is the latest
	- Get history image: `docker image history ${docker-image-name}:${docker-image-version}`
	- Remove image: `docker image rm ${docker-image-name}:${docker-image-version}`
	- Run image: `docker run ${docker-image-name} ash`
		- With the `ash` tag will create the ash like bash in linux for accessing container if users exit the ash the container will also stop.
	- 