- `docker image/container/volume prune` remove all unused images or container. ^b22ac4
## [[Docker#^7a8e6c|Image]]
^736b8f
- `docker build .` build the docker file from current folder and create an image.
	- `-t $name:$tag .` create an image with a name and tag.
	- `--build-arg $arg=$arg_value` assign argument variable. 
- `docker image remove $image_name/id` remove image, require all the container from that image is removed.
	- `docker rmi $image_name/id` alias.
	- [[CLI#^b22ac4|prune]]
- `docker image inspect $image_name/id`
- `docker push/pull $username/$image_name:$tag_name` push or pull the image from the **docker hub**. 
	- When pushing the image, docker will not push all the information about the whole image, it will only push extra information if the image is based on another image.
## [[Docker#^d2bd26|Container]]
^60c378
- `docker start $container_id/container_name` start created container with detach mode. ^1cc093
	- `-i` [[CLI#^ae9c17 | ref]]
	- `-a` [[CLI#^5cf14f | ref]]
- `docker run $image_id/image_name` create a new container from image in attach mode.
	- If the image already on your system, docker will not download the latest version of the image.
	- `-it ` create and keep alive **STDIN**, **STOUT** another call the interactive mode. ^ae9c17
	- `-p $computer_port:$exposed_port` map the port from container to your computer port.
	-  `-d` run in detach mode also apply for [[CLI#^1cc093|docker run]].
	- `-a` run in attach mode also apply for [[CLI#^1cc093|docker run]]. ^5cf14f
	- `--rm` remove container when finished.
	- `--name $container_name` create container with your assigned name.
	- `-v $local_volume:$container_volume` create a named volume for containers. If you don't always want to copy and use the full path, another calls for this command are: 
		- `-v $(pwd):container_volume` for macOS/Linux.
		- `-v "%cd%":container_volume` for Windows.
	- `-e $environment_variable:$environment_value` or `--env $environment_variable:$environment_value` create and assign an environment value. This will protect image from leaking security information by not including it into the image.
		- `--env-file $environment_file` set environment variables from a file.
- `docker stop $container_id/container_name` stop the running container.
- `docker attach $container` attach container terminal.
- `docker logs $container$` fetch the log of the container.
	- `-f` continue output log.
- `docker rm $container_name/id` remove container.
	- [[CLI#^b22ac4|prune]]
- `docker cp $a $b` copy file from local computer file/folder to running container or reverse.
	- `$a` and `$b` can be `$file/folder_on_computer` or `$container_name/id:$path`
## [[Volumes]]
^042429
- `docker volume rm $volume_name`
- `docker run -v $local_folder:$container_folder:ro` The `ro` command give folder readonly attribute where container can not write into this folder.