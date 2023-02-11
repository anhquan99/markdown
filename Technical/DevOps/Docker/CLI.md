- `docker image/container/volume prune` remove all unused images or container. ^b22ac4
## [[Docker#^7a8e6c|Image]]
^736b8f
- `docker build .` build the docker file  from current folder and create an image.
- `docker image remove $image_name/id` remove image, require all the container from that image is removed.
	- `docker rmi $image_name/id` alias.
	- [[CLI#^b22ac4|prune]]
- `docker image inspect $image_name/id`
- `docker build -t $name:$tag .` create image with a name and tag.
- `docker push/pull $username/$image_name:$tag_name` push or pull the image form the **docker hub**. 
	- When pushing the image docker will not push all the information about the whole image it will only pushe extra information if the image is based on another image.
## [[Docker#^d2bd26|Container]]
^60c378
- `docker start $container_id/container_name` start created container with detach mode. ^1cc093
	- `-i` [[CLI#^ae9c17 | ref]]
	- `-a` [[CLI#^5cf14f | ref]]
- `docker run $image_id/image_name` create a new container from image in attach mode.
	- If the image already on your system, docker will not download the latest vesion of the image.
	- `-it ` create and keep alive **STDIN**, **STOUT** another call the interactive mode. ^ae9c17
	- `-p $computer_port:$exposed_port` map the port from container to the your computer port.
	-  `-d` run in detach mode also apply for [[CLI#^1cc093|docker run]].
	- `-a` run in attach mode also apply for [[CLI#^1cc093|docker run]]. ^5cf14f
	- `--rm` remove container when finished.
- `docker stop $container_id/container_name` stop the running container.
- `docker attach $container` attach container terminal.
- `docker logs $container$` fetch the log of the container.
	- `-f` continue output log.
- `docker rm $container_name/id` remove container.
	- [[CLI#^b22ac4|prune]]
- `docker cp $a $b` copy file from local computer file/folder to running container or reverse.
	- `$a` and `$b` can be `$file/folder_on_computer` or `$container_name/id:$path`
- `docker run --name $container_name` create container with your assigned name.
	- `-v $local_volume:$container_volume` create a named volumes for containers. If you don't always want to copy and use the full path, another calls for this command are: 
		- `-v $(pwd):container_volume` for macOS/Linux.
		- `-v "%cd%":container_volume` for Windows.
## [[Volumes]]
^042429
- `docker volume rm $volume_name`