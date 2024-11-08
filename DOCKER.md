- Docker is software for deploying and running contanorized applications
	- Docker contains CONTAINERS
		- App runtime
		- Direct dependencies
		- Apps, Libs, system packages, FS
		- Has everything you need to run it
	- Containers have a limited way of communicating with each other, except for competing for system resources
- VM vs Container
	- Both provide isolation, control, and reproducibility
	- VMs are significantly slower and have a inefficient resource usage
		- essentially emulate the whole machine
	- They also eat up a lot of hard ware space, GBs, and have a 30s-1min+ startup time
	- Container on the other hand, have near native performance, minimal overhead, usually only a few mbs, and typically have a less than 1 sec startup time
- Containers vs Images
	- Images are templates for containers:
		- Specifies the filesystem, users, default command, and more
		- View it as the recipe to run the container
	- Container are the actual group of process that are launched and run based off what the image specifies
- Docker tags are mutable
`docker run nginx:1.27` 
this command grabs the 1.27.0 version. If 1.27.1 gets released, the 1.27 tag will point to the new one
- using the digest tag, we can guarantee that the images will not be update in the near future, and that we are grabbing the correct one
- Slim and Alpine images
	- Most images, such as Python, contain whole language and libraries to run it
	- tags such as slim and alpine, contain the bare bones needed to run the image.
		- alpine references a lightweight linux distro, that is even smaller than the defaul demian. It focuses on minimalism
		- alpine uses a few different things compare to Slim and regular linux versions:
			- One is using MuslLibc vs glibc
				- Benefits low-end hardware and takes less space
				- Uses Ash default shell over Bash
					- compatible, fast, light weight and fast.
					- Less interactive debugging and several other normal shell features
				- uses apk and .apk package management over the typical apt and .deb
					- Simpler and efficient, yet lacks features that can also work in a vast environment of machines

| Images | Size    |
| ------ | ------- |
| Python | 1.02 Gb |
| Slim   | 157 MB  |
| Alpine | 59 MB   |
- For debugging sessions add the line:
  docker exec -it (name or Id of container)
- Allows for a interactive debugging terminal for the container
- Currently in docker, there exists three types of mounts:
	- Volumes and Bind-mounts allow for persistent data storage
	- Tempfs mounts do not

| Volumes                                     | Bind-mounts                                                                      |
| ------------------------------------------- | -------------------------------------------------------------------------------- |
| newer                                       | older                                                                            |
| more features                               | less features                                                                    |
| managed by docker daemon                    | mounts host file/dir into container                                              |
| `-v mydata:/path/in/container` or `--mount` | `-v ./mydata:/path/in/container` or `-v /mydata:/path/in/container` or `--mount` |
- which mount do you want to use:
	- Bind-mounts are great if you are developing some code, since are you able to directly manipulate and interact with the data that the container is using
		- also allows to quickly share to the host
		- issue can be container now has direct access to the host
			- can be mitigated with read-only
	- Volumes are more appropriate in a production setting
		- They are not dependent on host filesystem and are easy to share across containers
		- Can use remote or cloud storage:
			- AWS EFS
			- NFS
			- SSHFS
		- Abstract away the file system of the host, allowing for less access by the container to the host
		- Not convenient when accessing the filesystem/data of the container
- Layers - they are like image diffs
	- Dockerfile runs the commands in order, and the docker image creates layers for each command, bases the new layer on the previous one created
	- Layers are immutable, so delete creates a new layer instead of editing one
	- Images are made of all the layers created
- Docker caches all of the commands, and whenever you run a new dockerfile, it checks the cache with the new commands
	- If anything changes, it will remove all the layers in the cache, and redownload everything
	- Essentially it goes in order, the lower the change is, the less layers/changes need to be done to the cache
	- if you do not want the cache to be used, use this:
	  `docker build --no-cache .`
	- A run commande that deletes something, does not delete the old layer, it instead creates a new layer
	- DO NOT PUT SENSITIVE INFO IN A DOCKER IMAGE
	- deleted data exists in prior layers.
- 