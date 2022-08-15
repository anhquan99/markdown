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
		- With the `ash` tag will create the ash like bash in linux for accessing container if users exit the ash the container will also stop, it is like access with session when exit the container end.
- Prune container: `docker container prune`
## Debug container
- `docker container exec -it ${container-name}` to create a session of a container and when exiting it will not stop the container 
- To exit a container, use `Ctr + P + Q`
- `docker attach ${container-name}` to attach a session of a container and exit will stop container
- `docker container log ${container-name}` to see container log
- `-it` is short for interactive terminal
## Network
- Docker networking![[Pasted image 20220815220045.png]]
- Docker `--network=none` when running the container network will be isolated
- Docker `--network=host`, the container will mount the port 8080 of the computer and there will no container can access this host
- Docker `--network=bridge`, docker will create a switch to connect containers together and this switch will forward the container port to a specifict port of the computer, this is a default network when the network is implicite.
- `docke run 8080:80 ${docker-image-name}` 8080 is the host port and 80 is the container port
## Volumes
- `docker run -v ${host-path}:${container-host}`
- ![[Pasted image 20220815223135.png]]
- Best practice we should not mount many containers to the same volume, it will make the application run into error when read or write.
## Workflow with Docker
![[Pasted image 20220815224504.png]]
## Docker file
- Docker file → Docker image
- Docker command `COPY` vs `ADD`
	- `COPY` copy from the host to the container.
	- `ADD` Copy from remote host and if the file is compress then it will extract all the files.
- `ENTRY-POINT` And `CMD`
		- 1 `ENTRY-POINT` per docker file
		- Last `CMD` is the main CMD all the previous CMD is pass.
![[Pasted image 20220815224750.png]]
- [Docker best practice](https://docs.docker.com/develop/develop-images/dockerfile_best-practice)
