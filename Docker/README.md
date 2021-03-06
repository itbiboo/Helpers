Docker - quick reference
========================

Docker Basics e-book: [Docker-for-Virtualization-Admin-eBook.pdf](Docker-for-Virtualization-Admin-eBook.pdf)

## Links
 - [Docker for beginners](https://prakhar.me/docker-curriculum/)
 - [MySQL Docker Containers: Understanding the basics](https://severalnines.com/blog/mysql-docker-containers-understanding-basics)
 - [What's the difference between RUN and CMD in a docker file and when should I use one or the other?](http://stackoverflow.com/questions/37461868/whats-the-difference-between-run-and-cmd-in-a-docker-file-and-when-should-i-use/37462208#37462208)
 - [What is the difference between CMD and ENTRYPOINT in a Dockerfile?](http://stackoverflow.com/questions/21553353/what-is-the-difference-between-cmd-and-entrypoint-in-a-dockerfile)
 
## Images
 - search for images on https://hub.docker.com
 - pull image example: `docker pull resin/rpi-raspbian`
 - list available (already pulled) images `docker images`
 - delete image example: `docker rmi resin/rpi-raspbian`

## Containers
### Run

```bash
#  Most of the times I used the following...
#  but it has problems because .profile is not used
#  and you have to do it manually
docker run -itd --name=CONTAINER_NAME ubuntu:TAG /bin/bash

#  To overcome this I started using the following:
docker run -itd --name=CONTAINER_NAME ubuntu:TAG bash -l

#  more info here:
#  http://stackoverflow.com/questions/38024160/how-to-get-etc-profile-to-run-automatically-in-alpine-docker
```

### Enter running Container with new TTY

This is very useful to cases where you have to start a program that starts printing logs in the standard output and you also want another CLI to do other stuff...
```bash
docker exec -it "id of running container" bash
```

### Misc

 - run a new container example: `docker run -it resin/rpi-raspbian`
    - docker creates a `new` container based on the image selected and assigns a random name, for example `lonely_keller`
    - run a new container providing name: `docker run --name ndpi_test -it resin/rpi-raspbian`
    - changes to this container (apt-get install... etc) will not affect future containers being generated based on the same image
 - start the container with: `docker start lonely_keller`
 - stop the container with: `docker stop lonely_keller`
 - rename a container with: `docker rename old_container_name new_container_name`
 - get access to the console of every running container with: `docker attach container_name`
 - return back to the host by pressing `Ctrl+p` and then `Ctrl+q`
 - list the running containers: `docker ps`
 - list all the containers: `docker ps -a`
 - remove container: `docker rm container_name`
 - remove all containers: `docker rm $(docker ps -a -q)`
 - maybe you will have to stop all containers first: `docker stop $(docker ps -a -q)`
 - remove all images: `docker rmi $(docker images -q)`
 - remove all untagged images `docker rmi $(docker images | grep "^<none>" | awk "{print $3}")` (source: [Remove Untagged Images From Docker](http://jimhoskins.com/2013/07/27/remove-untagged-docker-images.html)
 - remove all volumes: `docker volume rm $(docker volume ls -q)`
 - remove all volumes (safer): `$(docker volume ls -qf dangling=true)`
 - remove all volumes when docker version > 1.13.0 (2017-01-18): `docker volume prune`
 - display number of container restarts: `docker inspect -f "{{ .RestartCount }}" <container_id>`
  

## Commit images to Docker Hub
example:
```bash
docker commit temp_container tgogos/ffmpeg:bbbunny-480
docker push tgogos/ffmpeg:bbbunny-480
```

### Possible states
figure from: [https://docs.docker.com/engine/reference/api/docker_remote_api/](https://docs.docker.com/engine/reference/api/docker_remote_api/)
 ![docker events](event_state.png)


## Docker networking
[https://blog.docker.com/2016/01/webinar-qa-docker-networking/](https://blog.docker.com/2016/01/webinar-qa-docker-networking/)

`docker0` (172.17.0.1/16) is the default bridge network that new containers are attached to if nothing else is specified.
### Create a new network and run a container
```bash
docker network create frontend # (172.18.0.1/16)
docker network ls
docker run  -d --name rose --net=frontend busybox top
#busybox is the docker image to pull (if not already available) and then start with command "top"
docker exec rose ifconfig
```

### Create a new network and specify interface name
```bash
# There is a --opt option which can be used like this:
docker network create --opt com.docker.network.bridge.name=br_test test-net


# or to do the same within a docker-compose.yml file:
networks:
  test-net:
    driver: bridge
    ipam:
     driver: default
     config:
       - subnet: 172.100.0.0/16
    driver_opts:
      com.docker.network.bridge.name: br_test
```

### Ping the container
```bash
docker run --rm busybox ping -c 4 rose
# you get ping: bad address 'rose'
# the above is because of network isolation. By default docker0 is used so 'rose' cannot be found.
# it works only if network is specified, like this:
docker run --rm --net=frontend busybox ping -c 4 rose
```

### Create another network and connect the container
```bash
docker network create backend # (172.19.0.1/16)
docker network connect backend rose
docker exec rose ifconfig
```

### Disconnect from a network and destroy it
```bash
docker network disconnect backend rose
docker exec rose ifconfig
docker network rm backend
docker network ls
```

## Copy files from host to container
`docker cp /path/filename container_name:/path/filename`


## Expose Docker Remote API
```bash
cd /etc/systemd/system/docker.service.d
# create a file: remote-api.conf
sudo nano remote-api.conf

# add the following lines (without the #):
#   [Service]
#   ExecStart=
#   ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:4243 -H unix:///var/run/docker.sock

# reloading daemon definitions
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## apt-get install ... (packages sometimes needed)
```bash
#  ifconfig
apt-get install net-tools

#  ping
apt-get install iputils-ping

#  traceroute
apt-get install traceroute
```

## How to keep a container running

inside the `Dockerfile`:

```
CMD ./start.sh
```

inside the `start.sh`:

```
# (found here: http://stackoverflow.com/a/30209974/1561148)
tail -f /dev/null
```


## Environment Variables

example from: https://stackoverflow.com/questions/48402906/how-to-set-system-wide-environment-variables-in-dockerfile-from-a-file-inside-th

```
$ docker run -it alpine echo $HOME
/home/lars

$ docker run -it alpine echo '$HOME'
$HOME

$ docker run -it alpine sh -c 'echo $HOME'
/root
```
