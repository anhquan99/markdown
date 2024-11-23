- `FROM` specifies what image should docker use.
- `WORKDIR` specifies the working directory for docker whenever the container is created. It is not required, after the `WORKDIR` is specified docker will use that directory as default. ^c49566
- `COPY` copy files from the current folder of your computer to the container folder.
	- If some folder or files you don't want to copy the use the `.dockerignore` file.
- `EXPOSE $PORT` exposes the container port to network.
- `CMD[$command]` will be executed when the container is created.
- `VOLUME ['$volume_name']` create an anonymous volume when the container starts.
- `ENV $environment_variable $value` set default environment variable value.
- `ARG $arg=$arg_value` create a default argument.
- `ENTRYPOINT ["$COMMAND"]` create a default prefix command on each command user input. ^4e4524
- Optimize docker build:
	- Build time:
		- Place the most likely to change in the bottom of the file.
	- Size:
		- Choose image with slim or alpine tag.
- Scaling:
	-  Vertical scale: increase physical resources.
	- Horizontal scale : increase instances of application.
# `COPY` vs `ADD`
| `COPY`                                         | `ADD`                                                                                      |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Keep the original timestamps                   | Reset timestamps                                                                           |
| Not support extracting tar files automatically | Support extracting local tar files automatically                                           |
|                                                | Fetch and extract remote URLs, including tar files and compressed archives, into the image |
# `CMD` vs `ENTRYPOINT`
| `CMD`                                                               | `ENTRYPOINT`                                                                  |
| ------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Set the default command and/or parameters for the container         | Specify the command that will be run as the main process within the container |
| Can be overridden by providing a command when running the container | Can't be overridden by providing a command when running the container         |
- 3 forms:
	- Shell: `CMD echo "Hello world"`
	- Exec: `CMD ["echo", "Hello world"]`
	- JSON array:  `CMD ["/bin/sh", "-c", "echo", "Hello world"`
# `Dockerfile` good practice for Node and NPM
- Leverage non-root user.
- Set NODE_ENV=production by default.
- Install NPM dependencies before adding code.
- Use node (not NPM) to start the server.
```dockerfile
# Separate builder stage to compile SASS, so we can copy just the resulting CSS files.
FROM rubygem/compass AS builder
COPY ./src/public /dist
WORKDIR /dist
RUN compass compile

# Output: css/app.css
# Use NodeJS server for the app.
FROM node:12

# Copy files as a non-root user. The `node` user is built in the Node image.
WORKDIR /usr/src/app

RUN chown node:node ./
USER node

# Defaults to production, docker-compose overrides this to development on build and run.
ARG NODE_ENV=production

ENV NODE_ENV $NODE_ENV

# Install dependencies first, as they change less often than code.
COPY package.json package-lock.json* ./

RUN npm ci && npm cache clean --force

COPY ./src ./src

# Copy compiled CSS styles from builder image.
COPY --from=builder /dist/css ./dist/css

# Execute NodeJS (not NPM script) to handle SIGTERM and SIGINT signals.
CMD ["node", "./src/index.js"]
```
# Notes
- Optimize:
	- Size:
		- Multi-state build
		- Image tag
		- Docker.ignore
	- Build speed:
		- Caching
		- Image size: small image faster download
		- Copy unfrequently change file first (.csproj)