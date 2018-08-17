
# Using copy commands
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt



# Buid the docker image with name web
# Run this command where the Dockerfile located
>> docker build -t web

# Now do docker image ls to see the build image it will display web with TAG latest
>>> docker image ls 

# All the images, containers, file system and networks are installed in /var/lib/docker/ directory in linux system


# Sample app
+-- app.py
+-- Dockerfile
+-- requirements.txt


# Build the docker image using below command 
>> docker build -t web . # Get the Dockerfile description from curr dir and build an image. 

# Now run the build docker image, running instance of image is called container
>> docker run -p 4000:80 web

# Running the container in detached mode. 
>> docker run -d -p 4000:80 friendlyhello

# List the containers
>> docker container ls 

# Stop the container
>> docker container stop <container_id>


# Some usable docker commands
docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry


# Working with docker compose file to create a distsributed system to made scaling easy. 
# Example of docker-compose.yml file. 

version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image:web # Image that is created prevously
    deploy:
      replicas: 5 # create five replicas of web
      resources:
        limits:
          cpus: "0.1" # Assign only 10% cpu of host to each replica. so 10 *5 50% cpu of host will be used
          memory: 50M # Each replica use 50MB of ram so 10 * 50 = 500MB of ram of host will be used
      restart_policy:
        condition: on-failure
    ports:
      - "4000:80" # Expose the 80 port to 4000 to webnet load balancer.
    networks:
      - webnet
networks:
  webnet: # must required. 



# Build and up the container at same time.
>> docker-compose up --build 
# Deploy the application using docker stack command. 
>> docker stack deploy -c docker-compose.yml web


# View all the running services. 
>> docker services ls


# Use of swarm for scaling replices
sudo docker swarm init --advertise-addr 192.168.56.100


# Some docker commands. 
docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager



# What is docker swarm
Running the container in multiple machines. Multi-container, multi-machine applications are made possible by joining 
multiple machines into a Dockerized cluster called a swarm.

A swarm is a group of machines that are running Docker and joined into a cluster

The machines in a swarm can be physical or virtual. After joining a swarm, they are referred to as nodes.


# Setting up swarm. 
A swarm is made up of multiple nodes, which can be either physical or virtual machines

>> docker swarm init # Enable swarm mode and make your current machine a swarm manager

>> docker swarm join #   on other machines to have them join the swarm as workers.


# Docker machine
docker-machine is used to create the virtual machines.

>> docker-machine create --driver virtualbox myvm1
>> docker-machine create --driver virtualbox myvm2
>> docker-machine ls # list the virtual machines and get their IP addresses.

# Some commands to run docker swarm. 
docker-machine create --driver virtualbox myvm1 # Create a VM (Mac, Win7, Linux)
docker-machine create -d hyperv --hyperv-virtual-switch "myswitch" myvm1 # Win10
docker-machine env myvm1                # View basic information about your node
docker-machine ssh myvm1 "docker node ls"         # List the nodes in your swarm
docker-machine ssh myvm1 "docker node inspect <node ID>"        # Inspect a node
docker-machine ssh myvm1 "docker swarm join-token -q worker"   # View join token
docker-machine ssh myvm1   # Open an SSH session with the VM; type "exit" to end
docker node ls                # View nodes in swarm (while logged on to manager)
docker-machine ssh myvm2 "docker swarm leave"  # Make the worker leave the swarm
docker-machine ssh myvm1 "docker swarm leave -f" # Make master leave, kill swarm
docker-machine ls # list VMs, asterisk shows which VM this shell is talking to
docker-machine start myvm1            # Start a VM that is currently not running
docker-machine env myvm1      # show environment variables and command for myvm1
eval $(docker-machine env myvm1)         # Mac command to connect shell to myvm1
& "C:\Program Files\Docker\Docker\Resources\bin\docker-machine.exe" env myvm1 | Invoke-Expression   # Windows command to connect shell to myvm1
docker stack deploy -c <file> <app>  # Deploy an app; command shell must be set to talk to manager (myvm1), uses local Compose file
docker-machine scp docker-compose.yml myvm1:~ # Copy file to node's home dir (only required if you use ssh to connect to manager and deploy the app)
docker-machine ssh myvm1 "docker stack deploy -c <file> <app>"   # Deploy an app using ssh (you must have first copied the Compose file to myvm1)
eval $(docker-machine env -u)     # Disconnect shell from VMs, use native docker
docker-machine stop $(docker-machine ls -q)               # Stop all running VMs
docker-machine rm $(docker-machine ls -q) # Delete all VMs and their disk images


**
When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt.
This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

When a container is removed, any changes to its state that are not stored in persistent storage disappear.


>> $ docker run -i -t ubuntu /bin/bash
>> $ docker run alpine echo "Hello world" 
>> docker inspect alpine

Run the docker in detached mode. 
>> docker run -d dockersamples/static-site

Stop the docker
>> docker stop a7a0e504ca3e

Remove docker container.
>> $ docker rm   a7a0e504ca3e


>> $ docker run --name static-site -e AUTHOR="Your Name" -d -P dockersamples/static-site
e61d12292d69556eabe2a44c16cbd54486b2527e2ce4f95438e504afb7b02810

In the above command:

-d will create a container with the process detached from our terminal
-P will publish all the exposed container ports to random ports on the Docker host
-e is how you pass environment variables to the container
--name allows you to specify a container name
AUTHOR is the environment variable name and Your Name is the value that you can pass


# See all the ports of docker container.

>> docker port static-site

If you are using Docker Machine on Mac or Windows, you can find the hostname on the command line using docker-machine as 
follows (assuming you are using the default machine).

$ docker-machine ip default




An important distinction with regard to images is between base images and child images.

Base images are images that have no parent images, usually images with an OS like ubuntu, alpine or debian.

Child images are images that build on base images and add additional functionality.


>> docker stack services vote

# Kill, remove all the containers. 
stop all containers:
docker kill $(docker ps -q)

remove all containers
docker rm $(docker ps -a -q)

remove all docker images
docker rmi $(docker images -q)

remove all docker volumes
docker volume ls -qf dangling=true | xargs -r docker volume rm

remove the stack 
>> docker stack rm vote

remove docker image forcefully.
>> docker rmi 82 -f # 82 is the tag of the image. 



# Some container stuffs. 
Image is the binaries + libraries + source code that made app
container is the running instance of image, there can be many containers of image


>> docker run container run --publish 80:80 nginx

>> docker container ls (list the running containers) docker ps (old way to list running cont)

>> docker container stop 6390 # Stop the containers. 

>> docker container run nginx (always start the new container) 

>> docker container run nginx --name="nginx web server for pustakalaya" --detach --publish 80:8000

>> docker container logs webhost # To see the logs of webhost container which run in bg. 

>> docker container top webhost # See the process of containers. 

>> docker container --help 

>> docker container ls -a # List all the running commands 

>> docker container rm 63f 690 ode # remove the containers, make sure to stop before remove. 

>> docker container rm -f 63f # Forcely remove the command without stopping it. 

# See the log
>> docker logs container_name

# what happens when do docker run. 
- Look for image locally in image cache, if not pull from cloud(dockerhub, can change). 
- Download the latest version locally. 
- Create new container based on that image and prepare to start and customize networking


# What are containers.
- They are just the restricted process in host OS, not like virtual machine.

>> docker run mongo --name "mongo"
>> docker top mongo 

- If you do ps -aux | grep mongod  you will see that container mongo is running as a process in host. 


>> docker run mysql --detach --name "mysql" --env MYSQL_RANDOM_ROOT_PASSWORD=yes

# MYSQL_RANDOM_ROOT_PASSWORD, set the random root password in db, get that random 
pass word from the logs as shown in second command below.
>> docker container run --detach --publish 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql 


>> docker container logs db

# Run apache
>> docker container run -d --name webserver -p 8080:80 httpd 

# run proxy server
>> docker container run -d --nameproxy =p 80:80 nginx 

# Some other command 
>> docker container stats db
>> docker container inspect nginx 
>> docker container 


# Getting a shell inside container
# t: tty, i: interactively.

>> docker container run -it # start new container interactively
>> docker container exec -it nginx # run additional command in existing container. 

# Apline linux image is 4MB in size in docker hub. 

# Docker network concepts. 
>> docker container run -p // publish the port. 
>> docker container port nginx // display the list of port which are open.

# Docker network.
- In docker default network is bridge network, which is connected to nat firewall and routed
- ALl container on virtual network can talk to each other inside our host. 
- Best practice is to create a new virtual network for each app. 
   - network my_web_app
   - network my_api 
- bridge == docker0 
- docker0 is connected to host. 
- container are connected via bridge network. 


** Batteries included but removable** 
- Make multiple virtual networks, based on security requirements. 
- Use --net=host to connect directly to host. 

# Ip address of the container. 
>> docker container inspect --format '{{.NetworkSettings.IPAddress}}' webhost // here {{ are the go templates 

# Starting the stopped container docker
>> docker container start container_id




# Networking
>> docker network ls
>> docker network inspect
>> docker network create --driver
>> docker network connect
>> docker network disconnect 

>> sudo docker network create my_app_net # create the bridge network. 

>> docker container run -d --name new_nginx --network my_app_net nginx 

# It get the new network with ip of 172.18.0.0/16 18/19/20 incrementally.
>> docker netowrk inspect my_app_net

# Connet the webhost to new network with diff network and diff ip, 
# ip for the container is assigned via dhcp. 
>> docker network connect my_app_net mysql_server


# Working with Docker DNS. 
# How it affects custom n/w and default n/w. 
- Dns is the key to easy inter-container comms
- --link to enable dns on default dns

- Docker use container name as a hostname to talk to each other. 

>> docker network inspect my_app_net 

>> docker container run -d --name my_nginx --network my_app_net nginx 

>> docker container exec -it my_nginx ping new_nginx # ping from my_nginx to new_nginx. 

- The default bridge network has one disadv, it doesn't have the built-in dns. 
- Use --link options while creating a container to connect to ther network. 

- Container shouldn't rely on IP's for intercommunication
- DNS for firendly names built-in of yyou uses custom n/w

# Run two linux container (Centos, and Ubuntu) and check the curl version. 

- once there install curl and report the curl vresion. 


```
# Pull ubuntu
>> docker run -d --name "Ubuntu" ubuntu
or
// rm just remove the container wheny you exit bash shell.
>> docker run --rm -it ubuntu bash
>> docker run -d --name "centos" centos

>> docker container run --rm -it ubuntu:14:04 bash 
>> docker exec -it Ubuntu bash 
# apt-get install curl 
# curl --version

>> docker exec -it centos bash 
# yum install curl -y
# curl --version

```


# DNS roundrobin test. 
Dns round robin testing, you can have two diff host with dns alisa reponse to the
same name.
with docker engine 1.11 plus we can have multiple containers on a created network that 
responsed to the same dns address.

- create a new virtual network, default bridge driver.
- create two cotnainer from elasticsearch:2 image
- use --net-alias search command when creating them to give them an additional dns name to respond to. 

Step
====
>> docker network created dude
>> docker container run -d --net dude --net-alias search elasticsearch:2
>> docker container run -d --net dude --net-alias search elasticsearch:2 


Docker other stuff
====================
- b'Docker-Internals-cgroups-namespaces-and-beyond-YouTube-'
- b'https://youtu.be/sK5i-N34im8?list=PLBmVKD7o3L8v7Kl_XXh3KaJl9Qw2lyuFl'
- b'Dockers-format-option-for-filtering-cli-output'
- b'https://docs.docker.com/engine/admin/formatting/'
- b'Package-Management-Basics-apt-yum-dnf-pkg'
- b'https://www.digitalocean.com/community/tutorials/package-management-basics-apt-yum-dnf-pkg'




# Docker images
- app binaries + dependencies
- not a complete os, no kernel as host provide the kernal(Which is diff from vm)
- get the images from
	a. hub.docker.com


# Image layers
- Union file system
- history and inspect command in image to know what is image made up
- Every RUN command make a new layer, and also ENV variable. 
- We don't need to download the layers, which are already there. 
- COW: copy on write

>> docker images # To list the images. 
>> docker history nginx:latest # History of image layers.

# Image tagging. 
- Tag is the pointer to the specific image commit. 
- To pull the 8.0.0 mysql image dog. 
>> docker pull mysql:8.0.0
>> docker image tag nginx nix1947/nginx # Taging the image nginx 
>> docker image push nix1947/nginx # Upload the newly tagged image. 
>> docker login
>> docker logout

# Docker configuration/
$ cat ~/.docker/config.json


# Building the docker image, aka docker file.
- By default none of the commands are open by default, we need to exposet it.  
>> docker build -f <different_docker_File_or_than_Dockerfile> 

Five Basic building blocks of docker file. 

FROM debian:jessie
ENV NGINX_VERSION 1.11
RUN apt-get updated && apt-get intall nginx
EXPOSE 80 443
CMD ["nginx", "-g", "daemon off;"]


>> docker image build -t customnginx . # Build the image from the docker file in this dir.

- The thing that change the least should at the top of docker file, and the thing 
that change least should go at the bottom of the file. 

- All commands are re-run after the line which get changed. 


# Extending official images.

FROM nginx:latest
WORKDIR /usr/share/nginx/html
# using WORKDIR is prefereed to using 'RUN cd /some/path'


>> sudo docker image build -t nginx-with-html .
>> sudo docker build -t nginx
>> sudo docker container run nginx-with-html p 80:80 --rm  # Run container and on ctrlc remove it


# Building own docker file. 
- b'Images-and-Containers-From-Docker-Docs'
- b'https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/'
- b'List-of-Official-Docker-Images'
- b'https://github.com/docker-library/official-images/tree/master/library'
- b'Official-Docker-Image-Specification'
- b'https://github.com/docker/docker/blob/master/image/spec/v1.md'




# Persistent data. 
- Containers are ephemeral in desing and 
- Data volumes 
- Bind Mounts. 

"seperation of concerns" 
volumes: Make special location outside of container UFS to preserve data. 
Bind mounts: Link container path to host path. 

persistent data: volumes
-------------------------
volume need manual deletetion, it won't get removed on container removal.
- volume get the auto unique location in the host

>> docker volume ls 
>> docker volume inspect

- Named volume. 
>> docker container run -d --name mysql -v /var/lib/mysql/ # -v can also define in dockerfile using VOLUME dir. 

>> docker container run -d --name mysql -e MYSQL-ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql

>> docker volume create --help 


Persistent data using Bind Mounting
---------------------------------------
- Map a host file or directory to a container file or directory. 
- Binding is done in command line using container run command 
- -v is used for bind mount. 


>> docker container run -v /user/manoj/stuff: /path/to/container/ 

>> docker container run -d --name nginx -p 80:80 -v /home/manoj/mysite/src:/user/share/nginx/html nginx 




Database upgrade example
===========================
- database upgrdes with containers. 
- create a postgres container with 9.6.1 
- Upgrade to 10. 

- create a postgres container with named volume psql-data using version 9.6.1
- Use docker hub to learn VOlume path and versions needed to run it 
- Check logs, stop container
- Create a new postgres container with same named volume using 9.6.2
- Check logs to validate. 


>> sudo docker container run -d --name psql -v psql:/var/lib/postgresql/data postgres:9.6.1
>> sudo docker container logs -f psql  # view the logs
>> docker container stop psql 
>> sudo docker container run -d --name psql2 -v psql:/var/lib/postgresql/data postgres:9.6.2
>> docker volume ls # To see the volumes
>> docker container logs psql2 


Assignments with bind mounts for local dev
------------------------------------------------
- Use a jekyll static site generator to start a local web server
>> docker run -p 80:4000 -v $(pwd): /site/jekyll-serve
>> docker container run -it --name rubysanadbox -v ~/workspace/ruby_with_docker/:/code ruby:latest bash

- b'12-Fractured-Apps-A-follow-up-to-12-Factor-a-great-article-on-how-to-do-12F-correctly-in-containers-'
- b'https://medium.com/@kelseyhightower/12-fractured-apps-1080c73d481c#.cjvkgw4b3'
- b'Jekyll-a-Static-Site-Generator-just-as-background-info-no-need-to-install-'
- b'https://jekyllrb.com'


Docker compose
------------------
Yaml file describe, that describe
a. containers
b. networks
c. volumes

compose yamal format has its won version 1 2, 2.1, 3, 3.1
Swarm support v 1.13 

>> docker-compose --help
>> docker-compose -f my-docker-compose.yaml 

>> sudo docker-compose run web django-admin.py startproject composeexample .


```
version: '3.2' # If no version is specified then v1 is assumed

services:
  servicename: # Container name, used for dns resolution
    ima	ge: # Optional if you use build
    command: # optional, replace, default cmd specified by image
    environment: # optional, same as -e in docker run
    volumes: # same as -v in docker run
    
  servicename2:


volumes: # optional, same as docker volume create


networks: # Optional, same as docker network create.
```


# compose file for 
` docker run -p 80:4000 -v /home/mysite: /conainer/path conainer


services:
  jekyll:
    images: nginx
  volumes
    - .:/path/to/container/ 
   ports:
    - '80:4000' 



# Sample wordpress setup

version: "2.0"

services:
	wordpress:
		image:wordpress
		ports:
			-8080:80  # for multiple values it has - 
		environment:
			WORDPRESS_DB_PASSWORD: example
		volumes:
			- ./wordpress/data/:/var/www/html
	
	mysql:
		image:mariadb
		environment:
			MYSQL_ROOT_PASSWORD: example
		voLUMES:
		-./mysql-data:/var/lib/mysql







Docker compose
------------------
Not a production grade tool but ideal for local dev and test
>> docker-compose up # Setup /vol/network and start all containers.
>> docker-compose down # Stop all containers and remove /cont/vol/net.

If all your project had a Dockerfile and coker-compose.yml, then
netw developer onbaording would be

>> git clone github.com/someproject
>> docker-compose up


- b'-Docker-Docs-Compose-file-build-options'
- b'https://docs.docker.com/compose/compose-file/#build'
- b'Docker-Compose-Release-Downloads-good-for-Linux-users-that-need-to-download-manually-'
- b'https://github.com/docker/compose/releases'
- b'docker-compose-download-for-Linux-via-GitHub-Win-Mac-already-have-it.'
- b'https://github.com/docker/compose/releases'



# Swarm 
========
- Containers everywhere = new problems. 
- How do we automate container lifecycle. 
- Blue green deploy. 
- How can we control/track where container get stated
- How can we create cross-node virtual networks 
- How can we ensure only trusted servers run our containers
- How can we store private configuration. 

Swarm Mode
------------
- Brand new features. 
- Swarm mode is a clustering solution build inside a docker, that bring
into a single managable unit. 
- Swarmkit toolkit. 
- Enhanced stacks and secrets.
- Not enabled by default, new commands once enabled. 

- Raft consensus group. 
- Must run the same docker version. 
- Single service can have multiple task, and each task can run multiple containers. 
- New swarm api. 


# Swarm services
>> sudo docker info # TO check swarm enable on or not
>> docker swarm init # Initilize swarm mode.

Lots of pki and security automation
root signing certificate created for our swarm
certificate is issued for first manager node
join tokens are created

Raft database created to store root CA, configs and secrets
- Encrypted by default on disk (1.13+)
- No need for another key/value system to hold orchestration/secrets
- Replicates logs amongst managers via mutual TLS in control panel.


# some node commands
>> sudo docker node ls # Display the list of managers
>> sudo docker node --help
>> sudo docker swarm --help 
>> docker service create alpine ping 8.8.8.8 
>> docker service ls 
>> docker service ps # List the services, services can have many containers

>> docker service update <service_id> --replicas 3 

>> docker service ls # List the 3 containers of this 

# service can only run on swarm node. 

>> docker update  --help # Help to control resources usages ( mempry, cpu)
>> docker service update --help 
>> docker container ls 
>> docker container rm -f <some_id> 

# To remove all the containers, remove the services.
>> docker services ls. 


# Create a swarm cluster. 
- Multiple OS. 
- 3 Node swarm across all nodes. 
- play-with-docker.com
	- only nees a browser, but resets after 4 hours. 


>> docker-machine create node1

>> docker-machine ssh node1 

>> docker-machine env node1

>> docker node update --role manager node2 # Make the manager.

>> docker node ls  # display the list of manager

>> docker swarm join-token manager # List the token manager

# Create the services in node manager
>> docker service create --replicas 3 alpine ping 8.8.8.8 
>> docker services ls 
>> docker node ps node2 # Give the list of container running on node 2
>> docker service ps # List the services. 

- b'Configure-SSH-for-Saving-Options-for-Specific-Connections'
- b'https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client'
- b'Deploy-services-to-a-swarm-Docker-Docs-'
- b'https://docs.docker.com/engine/swarm/services/'
- b'Raft-Consensus-Visualization-Our-Swarm-DB-and-how-it-stays-in-sync-across-nodes-'
- b'http://thesecretlivesofdata.com/raft/'



# Overlay multi host networking
>> docker network create --driver overlay # For intra swarm comm. 
- for container to container traffic inside single swarm
- optional ipsec(AES) encryption on network creation
- Each service can be connected to multiple networks. 
	(front-end, back-end)
-
>> docker network create --driver overlay db_nodes
>> docker network ls 

>> docker service create --name psql --network db_nodes -e POSTGRES_PASSWORD=mypass postgres

>> docker service ls 

>> docker service ps psql 

>> docker container logs psql 

# Create web container services
>> docker service create --name drupal --network mydrupal -p 80:80 drupal 

>> docker services ps drupal # Get info where drupal containers get run. 

>> docker services ps psql # Get info where psql containers get run in diff nodes. 


Swarm routing mesh
===================
Routes ingress(incomming) packets for a service to proper task
- smapns all nodes in swarm
use IPVS from linux kernel
- load balances swarm services across their tasks
- Two ways this works
- contaniier to container in overlay network (use VIP: virtual ip)
- services has the virtual IP
- service name is itself dns. 

# create the elastic search services with 3 replica
>> docker service create --name search --replicas 3 -p 9200:9200 elasticsearch:2

# List where there nodes goes
>> docker service ps search 

- This is stateless load balancing.
- Elastic load balancer. 


# Create a multi service app. 
Using docker distributed voting app. 



Production grades compose: Stacks
==================================



Docker other tutorial
-----------------------
https://docker-curriculum.com/



Some docker compose commands
================================
docker-compose up # Bring all services up 
docekr-compose down # Stop all container removes, networks and volumes
docker-compose pause # Pause all services
docker-compose scale ..
ch


