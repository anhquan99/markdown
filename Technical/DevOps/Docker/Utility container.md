## Idea
- Use a container to execute something hasn't effected on the host machine without install all the requirement in the host machine.
- You can bind mount to share generated files/folders from container to local machine.
- You can use advantage of [[Dockerfile#^4e4524|ENTRYPOINT]] for default prefix user input command.
```shell
# this command is to create and install the npm package.json where the local machine doesn't have to install nodejs
# you specify the ENTRYPOINT in the Dockerfile as `npm` so after the image name you just need to input the `init`
# the project folder is mount to container folder then whenever a file or folder is generated, the project folder will have those files and folders
# docker containers will be shutdown after command is done
docker run -it -v $(pwd):/app util-node init
```