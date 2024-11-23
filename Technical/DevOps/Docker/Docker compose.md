# Docker compose
## Definition
- A orchestration command for building and running multiple docker container in the CLI.
- Docker compose is not suited for managing multiple containers on different hosts (machines)
- Docker compose default delete all the container when docker container stop.
- If you have a single value, you need a dash in the starting line, but if you have multiple key-value pairs, then you don't need a dash.
```yaml
environment:
	MONGO_INITDB_ROOT_USERNAME: aq # key value pair
	MONGO_INITDB_ROOT_PASSWORD: password
	- MONGO_INITDB_ROOT_USERNAME=aq # single value
```
- Docker will create a default network for all container when use docker compose, you don't need to specify the network when using docker compose.
- `working_dir: $container_path` set a default working directory like [[Dockerfile#^c49566|WORKDIR in Docker file]].
- `entrypoint: ["$command"]` set a prefix command for command line in container like [[Dockerfile#^4e4524|ENTRYPOINT in Docker file]].
- If a server is depended on other services, the docker will run the other services first, then the depending on service. This make you just need to run 1 service, then other services will run flow.
## [[CLI#^79c0c7|CLI]]
```yaml
version: '3.8'
services:
	mongodb:
		container_name: mongodb
		image: 'mongo'
		volumes:
		- data:/data/db
		# environment:
		# # MONGO_INITDB_ROOT_USERNAME=aq can be written in another way
		# MONGO_INITDB_ROOT_USERNAME: aq
		# MONGO_INITDB_ROOT_PASSWORD: password
		env_file:
		- ./env/mongo.env
	backend:
		container_name: backend
		build: ./backend # find a Dockerfile in the folder and create an image from that Dockerfile
		# another call for building image
		# build:
		# context: ./backend
		# dockerfile: Dockerfile # if your Dockerfile is in another folder
		# arg:
		# arg_name: arg_value
		ports:
		- '80:80'
		# if you container need more port continue specify the port
		volumes:
		- logs:/app/logs
		- ./backend:/app
		- /app/node_modules
		env_file:
		- ./env/backend.env
		# if the container depends on another containers, example backend server depends on mongodb
		# if the backend starts first then the mongodb starts after then the backend server is shutdown because of connection error is thrown.
		depends_on:
		- mongodb
	frontend:
		container_name: frontend
		build: ./frontend
		ports:
		- '3000:3000'
		volumes:
		- ./frontend/src:/app/src
		stdin_open: true # this service need input connection
		tty: true
		depends_on:
		- backend

# every NAMED volume it must be registered in here
volumes:
	data:
	logs:
```
- Docker-compose should not be used in production because of the scaleability could not create new instances.