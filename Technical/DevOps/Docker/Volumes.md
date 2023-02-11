- Volumns are folders on your host machine hard drive which are mounted into containers.
- If container is removed but the volumes will not be removed.
## External data storage
- <mark style="background: #ADCCFFA6;">Volumes (manged by docker):</mark>
	- <mark style="background: #D2B3FFA6;">Anonymous volumes:</mark> when user defines a mapped folder in container but don't define a volume for the host machine, Docker will automatically assign an anonymous volumes. This type of volumes are attached to the container, if the container is deleted it will be deleted.
	- <mark style="background: #D2B3FFA6;">Named volumes:</mark> user define volumes, and it is not attached with the container, but it is created and managed by [[Docker]].
	- <mark style="background: #D2B3FFA6;">Bind mounts:</mark> great for persistent, editable (by you) data. It is local machine folder map to the container folder.
## Folder mapping
- The local host machine folder will override the container folder, <mark style="background: #FF5582A6;">but docker will not do the reverse.</mark>
- Hence, this will make all the folder in the container will be overridden. In some cases, you don't want some folder to be overridden, like when then installed package from node_modules folder of Node.js, ... For those cases, the anonymous volume is to come in handy, it will prevent the folder from overridden. You can declare the anonymous volume in [[Dockerfile]] or in the [[CLI]].
## [[CLI#^042429|CLI]]
```shell
# example of mapping volume
# code change can be apply imediately
# all the created data can be store in some named volume and will not be deleted when container is removed
# installed packaged will not be overridden by mapping local machine folder, by using anonymous volume
docker run
	-d # detach
	-p 3000:80 # expose port
	--rm # remove when finished
	--name feedback-app # create container with name
	-v feedback:/app/feedback # map feedback (if not created then create volume) volume to the container folder
	-v $(pwd):/app # map current folder on local machine to container folder, for code change.
	-v /app/node_modules # map anonymous volume to container folder which will not be overridden by local machine, for install package not to be overridden.
	feedback-node:volume
```


