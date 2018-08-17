1. [Docker Installation](#install-docker) 
2. [Docker Notable commands](#docker-commands)
3. [Creating docker image](#creating-docker-image)
4. [Example: Creating django image to run django app](#)
5. [Docker Compose quickstart](#)
6. [Persisting data in docker container]
7. [Networking in docker]
8. [Services in docker]
9. [Docker and continious integration]
10. [Automation with docker and ansible]



# Docker Installation
https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository




# Docker commands
```bash
>> docker info # To get the docker version, list total images as well.
>> docker version 
>> docker image # List the locally available images
>> docker container ls # List the running containers
>> docker ps # List running containers
>> docker ps -a # List running and stopped containers
>> docker pull hello-world # Pull docker image from docker hub
>> docker login # Login to docker hub
>> docker run -it python:latest bash # Pull python latest image from docker hub, if not locally available, and run the bash shell interactively. 

>> docker run web -d --name mycontainer # Run web container in background 
>> docker run web ls -l # Run web container and print the output of ls -l command
>> docker port mycontainer # List the listening port of mycontainer 

# Run the djangoweb container from django image and pass environment varables as well and 
# Mapp all the exposed port to host.
>> docker run --name djangoweb -e DJANGO_SETTINGS_MODULE="settings.production" -d -P django

```

# Creating docker image


