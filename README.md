# Introduction to Docker and Kubernetes
## Installing Docker: The Fast Way
If you don't already have docker installed, Docker already has some great guides on how to do it. The rest of this Section is about how to setup Docker on your specific OS, but if you already know which OS you want to install on, here's the short-list for downloading it. The videos after this Lecture are walkthroughs of installing Docker, getting the GitHub repo, getting a code editor, and tweaking the command line if you want to. Feel free to skip any and all of this if you have at least docker version 17.06 and like your current setup :)

### Installing on Windows 10 (Pro or Enterprise)

This is the best experience on Windows, but due to OS feature requirements, it only works on the Pro and Enterprise editions of Windows 10 (with latest update rollups). You need to install "Docker for Windows" from the Docker Store.

With this Edition I recommend using PowerShell for the best CLI experience. See more info in the next few Lectures.

### Installing on Windows 7, 8, or 10 Home Edition

Unfortunately, Microsoft's OS features for Docker and Hyper-V don't work in these older versions, and "Windows 10 Home" edition doesn't have Hyper-V, so you'll need to install the Docker Toolbox, which is a slightly different approach to using Docker with a VirtualBox VM. This means Docker will be running in a Virtual Machine that sits behind the IP of your OS, and uses NAT to access the internet.

NOTE FOR TOOLBOX USERS: For all examples that use http://localhost , you'll need to replace with http://192.168.99.100

#### Important links for Windows
- [Install Docker Desktop on Windows](https://docs.docker.com/desktop/windows/install/)
- [Docker Hub](https://hub.docker.com/)
- [Github Desktop](https://desktop.github.com/)
- [VS Code](https://code.visualstudio.com/)
- [cmder](https://cmder.net/)
- [Docker for Windows FAQ](https://docs.docker.com/desktop/faqs/)
- [DockerCompletion](https://github.com/matt9ucci/DockerCompletion)
- [Getting Started](https://docs.docker.com/desktop/windows/)
### Installing on Mac

You'll want to install Docker for Mac, which is great. If you're on an older Mac with less than OSX Yosemite 10.10.3, you'll need to install the Docker Toolbox instead.

#### Important Links for Mac
- [Download and Insall Docker](https://docs.docker.com/desktop/mac/install/)
- [Github Desktop](https://desktop.github.com/)
- [VS Code](https://code.visualstudio.com/)
- [iTerm2](https://iterm2.com/)
- [Logs and Troubleshooting](https://docs.docker.com/desktop/mac/troubleshoot/)
- [User manual](https://docs.docker.com/desktop/mac/)


### Installing on Linux

Do *not* use your built in default packages like apt/yum install docker.io  because those packages are old and not the Official Docker-Built packages.

I prefer to use the Docker's automated script to add their repository and install all dependencies: curl -sSL https://get.docker.com/ | sh  but you can also install in a more manual method by following specific instructions on the Docker Store for your distribution, like this one for Ubuntu.

### Terminal Shell and Tab Completion for Docker CLI
Docker Desktop for Windows Terminals
On Windows you'll likely want to use PowerShell terminal (PowerShell.exe) rather than Command Prompt (cmd.exe), but if you don't like the default GUI window that Microsoft provides, you might want to check out cmder, which can run PowerShell inside a fancy tab-based GUI with lots of features. You can also use Bash shells but you're on your own for setting those up.

#### Windows PowerShell Tab Completion
Tab completion lets you quickly fill the rest of a command or argument as you type by hitting Tab. To get "tab completion" in PowerShell, try the setup steps for "DockerCompletion" on GitHub.

####  macOS Terminals
On Mac you can use the built in Terminal program which uses the default Bash shell. I prefer to use iTerm as my GUI terminal interface to gain more features than the built in one.

#### macOS Bash Tab Completion
Tab completion lets you quickly fill the rest of a command or argument as you type by hitting Tab. Assuming you're running Docker for Mac, you'll want to connect up the completion scripts that come with Docker for Mac to your shell. Docker talks about this in their docs.  1. install docker 2. install completion with brew 3. update your .bash_profile 4. symlink your docker completion files 5. restart your terminal.

#### Linux
In a desktop Linux distro like Ubuntu, Mint, etc. you'll likely need to do nothing. They already have good built-in terminals and Bash tab completion works out of the box. Happy days!


### What if None Of These Options Work

Maybe you don't have local admin, or maybe your machine doesn't have enough resources. Well the best free option here is to use play-with-docker.com, which will run one or more Docker instances inside your browser, and give you a terminal to use it with. You can actually create multiple machines on it, and even use the URL to share the session with others in a sort of collaborative experience.  I highly recommend you check it out.  Most of the lectures in this course can be used with "PWD", but it's only limitation really is it's time bombed to 4 hours, at which time it'll delete your servers.

## Docker Version Format Change
TL;DR: Versions are now YY.MM  based (like Ubuntu) and you can choose *Stable* (slower) or *Edge* (faster) releases. This means that newer features drop faster in Edge releases, and fixes are backported for a longer timeline to Stable releases. Everyone wins.

There may be mentions of 1.12  and 1.13  in this course, which were the two versions before 17.03 . 1.13.1  is actually the same features as 17.03.0 , so if you see 1.13  on my screen recording and 17.04  or similar in your CLI, it's fine!

In 2017 the Docker versioning, release cycle, and product names changed, but features haven't. Full details are here, but my commentary on important highlights are below:

- Docker Engine (the free one we all know and love) is now Docker CE (Community Edition). Notice the drop of the word Engine. I suspect we can just call this Docker now. It includes the cli client and backend daemon/service and api (just like it did before). You can get it the same ways as before.
- Docker Data Center is now Docker EE (Enterprise Edition) and adds additional paid products and support on top of Docker in three pricing tiers.
- These changes don't affect Docker Compose or Docker Machine.
- Docker's version is now YY.MM  based, using the month of its expected release, and the first one will be 17.03.0 , which is "the first point release" of the 17.03  release. If any bug/security fixes are needed to that release, the first update will be 17.03.1  etc.

We now have two release tracks (called variants) "Edge" and "Stable".

- Edge is released monthly and supported for a month.

- Stable is released quarterly and support for 4 months.

- You can extend Stable's support and backported fixes through a Docker EE subscription.

![eesubscription](imgs/eesubscription.png)


## Creating and using containers
In this section:
- Check versions of our docker CLI and engine
- Create a Nginx container
- Learn common container management commands
- docker networking basics


### Docker install and Config
- Open your favorite terminal based on your operating system (I'll use iTerm) and run:
  - `docker version` to get the Version of docker running on the machine
  - `docker info` provides a more detailed information about the Docker instance running on your machine.
- Docker commands format :
  - new: `docker <command> <sub command> options` (e.g `docker container run`)
  - old: `docker command options` (e.g. `docker run`)

### Starting a Nginx web server
#### Image vs container
- An Image is the application we want to run
- A Container is an instance of that image running as a process
- You can have many containers running off the same image
- In this lecture our image will be the Nginx web server
- Docker's default image "registry" is called Docker Hub  (hub.docker.com)

- Start a new container using: `docker container run --publish 80:80 nginx`
  - Open http://localhost in your browser
- Downloaded image 'nginx' from Docker Hub
- Started a new container from that image
- Opened port 80 on the host IP
- Routes that traffic to the container IP, port 80

```
docker container run --publish 80:80 --detach nginx

docker container ls

docker container stop 690

docker container ls

docker container ls -a

docker container run --publish 80:80 --detach --name webhost nginx

docker container ls -a

docker container logs webhost

docker container top

docker container top webhost

docker container --help

docker container ls -a

docker container rm 63f 690 ode

docker container ls

docker container rm -f 63f

docker container ls -a
```

#### What happens when we run a container?
- Looks for that image locally in image cache, doesn't find  anything
- Then looks in remote image repository (defaults to Docker Hub)
- Downloads the latest version (nginx:latest by default)
- Creates new container based on that image and prepares to  start
- Gives it a virtual IP on a private network inside docker engine
- Opens up port 80 on host and forwards to port 80 in container
- Starts container by using the CMD in the image Dockerfile

### Container Vs Virtual Machine
- Containers aren’t Mini-VM’s
- They are just processes
- Limited to what resources they can access (file paths, network  devices, running processes)
- Exit when process stops
```
docker run --name mongo -d mongo
docker top mongo
ps aux
docker stop mongo
ps aux
```

### Assignment: Manage Multiple Containers
- docs.docker.com and --help are your friend
- Run a nginx, a mysql, and a httpd (apache) server
- Run all of them --detach (or -d), name them with --name
- nginx should listen on 80:80, httpd on 8080:80, mysql on 3306:3306
- When running mysql, use the --env option (or -e) to pass in
- MYSQL_RANDOM_ROOT_PASSWORD=yes
- Use docker container logs on mysql to find the random password  it created on startup
- Clean it all up with docker container stop and docker  container rm (both can accept multiple names or ID's)
- Use docker container ls to ensure everything is correct before  and after cleanup


#### Solution
```
docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes MYSQL_RANDOM_ROOT_PASSWORD

docker container logs db

docker container run -d --name webserver -p 8080:80 httpd

docker ps

docker container run -d --name proxy -p 80:80 nginx

docker ps

docker container ls

docker container stop TAB COMPLETION

docker ps -a

docker container ls -a

docker container rm

docker ps -a

docker image ls
```

### What's Going on in containers: CLI Process Monitoring
- docker container top - process list in one container
- docker container inspect - details of one container config
- docker container stats - performance stats for all containers

```
docker container run -d --name nginx nginx

docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql

docker container ls

docker container top mysql

docker container top nginx

docker container inspect mysql

docker container stats --help

docker container stats

docker container ls
```

### Getting a Shell inside containers: No Need for SSH
- docker container run -it - start new container interactively
- docker container exec -it - run additional command in existing container
- Different Linux distros in containers

```
docker container run -help

docker container run -it --name proxy nginx bash

docker container ls

docker container ls -a

docker container run -it --name ubuntu ubuntu

docker container ls

docker container ls -a

docker container start --help

docker container start -ai ubuntu

docker container exec --help

docker container exec -it mysql bash

docker container ls

docker pull alpine

docker image ls

docker container run -it alpine bash

docker container run -it alpine sh
```

### Docker Networks: Concepts
- Review of docker container run -p
- For local dev/testing, networks usually "just work"
- Quick port check with docker container port <container>
- Learn concepts of Docker Networking
- Understand how network packets move around Docker

#### Docker Networks Defaults
- Each container connected to a private virtual network "bridge"
- Each virtual network routes through NAT firewall on host IP
- All containers on a virtual network can talk to each other  without -p
- Best practice is to create a new virtual network for each app:
  - network "my_web_app" for mysql and php/apache containers
  - network "my_api" for mongo and nodejs containers
- "Batteries Included, But Removable"
- Defaults work well in many cases, but easy to swap out parts to  customize it
- Make new virtual networks
- Attach containers to more then one virtual network (or none)
- Skip virtual networks and use host IP (--net=host)
- Use different Docker network drivers to gain new abilities


```
docker container run -p 80:80 --name webhost -d nginx

docker container port webhost

docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost
```


#### Docker Networks: CLI	Management
- Show networks `docker network ls`
- Inspect a network `docker network inspect`
- Create a network `docker network create --driver`
- Attach a network to container `docker network connect`
- Detach a network from `container docker network disconnect`
```
docker network ls

docker network inspect bridge

docker network ls

docker network create my_app_net

docker network ls

docker network create --help

docker container run -d --name new_nginx --network my_app_net nginx

docker network inspect my_app_net

docker network --help

docker network connect

docker container inspect TAB COMPLETION

docker container disconnect TAB COMPLETION

docker container inspect
```

#### Docker Networks: DNS
- Understand how DNS is the key to easy inter-container comms
- See how it works by default with custom networks
- Learn how to use --link to enable DNS on default bridge  network

- Containers shouldn't rely on IP's for inter-communication
- DNS for friendly names is built-in if you use custom networks
- You're using custom networks right?
- This gets way easier with Docker Compose in future Section

```
docker container ls

docker network inspect TAB COMPLETION

docker container run -d --name my_nginx --network my_app_net nginx

docker container inspect TAB COMPLETION

docker container exec -it my_nginx ping new_nginx

docker container exec -it new_nginx ping my_nginx

docker network ls

docker container create --help
```

### Assignment: Using Containers for CLI Testing.
- Use different Linux distro containers to check curl cli tool version
- Use two different terminal windows to start bash in both centos:7 and ubuntu:14.04, using -it
- Learn the docker container run —rm option so you can save  cleanup
- Ensure curl is installed and on latest version for that distro ubuntu: apt-get update && apt-get install curl centos: yum update curl
- Check curl --version

#### Solution
```
docker container run --rm -it centos:7 bash

docker ps -a

docker container run --rm -it ubuntu:14.04 bash

docker ps -a
```

### Bug in alpine affects nslookup
In the next assignment, you'll be using the tool nslookup inside the alpine:latest image, but in early 2020 there was a bug introduced to the latest Alpine image 3.11.3 that affects how nslookup works on hostnames, so for the next Assignment on DNS Round Robin, either change your command to work around it with nslookup search. (with a dot added) or use an older Alpine image like alpine:3.10.



Hopefully, they'll fix it soon. I'm tracking this bug: https://github.com/gliderlabs/docker-alpine/issues/539

### Assignment: DNS Round Robin Testing
- Know how to use -it to get shell in container
- Understand basics of what a Linux distribution is like Ubuntu  and CentOS
- Know how to run a container
- Understand basics of DNS records

Task:
- Ever since Docker Engine 1.11, we can have multiple containers  on a created network respond to the same DNS address
- Create a new virtual network (default bridge driver)
- Create two containers from elasticsearch:2 image
- Research and use —network-alias search when creating them  to give them an additional DNS name to respond to
- Run alpine nslookup search with --net to see the two  containers list for the same DNS name
- Run centos curl -s search:9200 with --net multiple times  until you see both "name" fields show

#### Solution
```
docker network create dude

docker container run -d --net dude --net-alias search elasticsearch:2

docker container ls

docker container run --rm -- net dude alpine nslookup search

docker container run --rm --net dude centos curl -s search:9200

docker container ls

docker container rm -f TAB COMPLETION
```
## Container Images: Where to find them and how to build them
### What's	In	An Image and What Isn't
- App binaries and dependencies
- Metadata about the image data and how to run the image
- Official definition: "An Image is an ordered collection of root  filesystem changes and the corresponding execution  parameters for use within a container runtime."
- Not a complete OS. No kernel, kernel modules (e.g. drivers)
- Small as one file (your app binary) like a golang static binary
- Big as a Ubuntu distro with apt, and Apache, PHP, and more  installed

![](imgs/container-images.png)
### Image Creation	and Storage
- Created Using a Dockerfile
- Or committing a containers changes back to an image
- Stored in Docker Engine image cache
- Move images in/out of cache via:
- local filesystem via tarballs
- push/pull to a remote "image registry" (e.g. Docker Hub)
- Images aren't ideal for persistent data
- Mount a host file system path into container
- Use docker volume to create storage for unique/persistent data


### Image Highlights
- Images are made up of app binaries, dependencies, and  metadata
- Don't contain a full OS
- Usually use a Dockerfile recipe to create them
- Stored in your Docker Engine image cache
- Permanent Storage should be in a Image Registry
- Image's don't usually store persistent data

```
docker image ls

docker pull nginx

docker pull nginx:1.11.9

docker pull nginx:1.11

docker pull nginx:1.11.9-alpine

docker image ls
```

### Image and Their Layers
- Images are made up of file system changes and metadata
- Each layer is uniquely identified and only stored once on a host
- This saves storage space on host and transfer time on push/pull
- A container is just a single read/write layer on top of image
- docker image history and inspect commands can teach  us

![](imgs/container-and-layers.png)
```
docker image ls

docker history nginx:latest

docker history mysql

docker image inspect nginx
```


### Image Tagging and Pushing to Docker Hub
```
docker image tag -- help

docker image ls

docker pull mysql/mysql-server

docker image ls

docker pull nginx:mainline

docker image ls

docker image tag nginx saikiranchalla/nginx

docker image tag --help

docker image ls

docker image push saikiranchalla/nginx

docker --help

docker login

cat .docker/config.json

docker image push saikiranchalla/nginx

docker image push saikiranchalla/nginx saikiranchalla/nginx:testing

docker image ls

docker image push saikiranchalla/nginx:testing

docker image ls
```
