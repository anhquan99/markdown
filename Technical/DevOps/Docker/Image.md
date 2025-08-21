# Image
- Pull a docker image command `docker run image_name:version` or `docker pull image_name:version`
- Alias
	- `image_name:version` : `IMG_V`
	- `image_name` : `img`
- Run interactive shell `docker run -it img`
- Start with [[dockerfile]], you will need to write code to tell docker how to build docker image.
- A image is built on instruction layers - layer architecture, every instruction a layer. When you build the image, docker will cache the layers for the rebuild process, this mean if no changes for in the code the image is built faster. Each layer stacked, if one changes others will be rebuilt.
```dockerfile
# example for the layer architechture

FROM node
WORKDIR /app
COPY . /app
# the npm install is run everytime the code change
# this will make use of docker cache 
RUN  npm install
EXPOSE 80
CMD ["node", "server.js"]

# refactor
FROM node
WORKDIR /app
# whenever package.json is changed, npm install is executed
COPY package.json /app
RUN  npm install
# result whenever the code changes it will not install npm agai, make the rebuild process is faster.
COPY . /app
EXPOSE 80
CMD ["node", "server.js"]
```
- Tag allows you to use the right version of the image.
- Sharing an image:
	- Share a Dockerfile (**Dockerfile instructions might need surrounding files/folders like source code,...**).
	- Share a Built image.
## [[cli#^736b8f|CLI]]