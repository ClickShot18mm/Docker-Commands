# DOCKER COMMANDS

A list of my commonly used Docker commands.


## Installation
```git
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

## CHECK DOCKER VERSION
```git
docker version
```

## Before Going Deeper, We Are Going To Update Some Configuration In Our Bash File By Running Few Command:
```git
sudo apt install net-tools
sudo apt install jq
```
```git
sudo vi ~/.bashrc
```

Paste The Below Code At The Bottom Of The bashrc File
```git
dps()  {
  docker ps $@ --format "table{{ .ID}}\\t{{ .Image }}\\t{{ .Names }}\\t{{ .Status }}\\t{{ .Command }}\\t{{ .Status }}\\t{{ .Ports }}" | awk '
  NR % 2 == 0 {
    printf "\033[0;32m";
  }
  NR % 2 == 1 {
    printf "\033[0;34m";
  }
  NR == 1 {
    printf "\033[1;31m";
    PORTSPOS = index($0, "PORTS");
    PORTS = "PORTS";
    PORTSPADDING = "\n";
    for(n = 1; n < PORTSPOS; n++)
    PORTSPADDING = PORTSPADDING " ";
  }
  NR > 1 {
    PORTS = substr($0, PORTSPOS);
    gsub(/, /, PORTSPADDING, PORTS);
  }
  {
    printf "%s%s\n\n", substr($0, 0, PORTSPOS - 1), PORTS;
  }
  END {
    printf "\033[0m";
  }
  '
}
dpsa() { dps -a $@; }


dpsjson()  {
  docker ps --format "{{json .}}" --no-trunc | jq
}
```

```git
source ~/.bashrc
dps
dpsjson
```


## RUN A CONATINER FROM AN IMAGE
Run an instance of the image on the docker host, if image is not on the host it will go to docker hub and pull

PATTERN | DESCRIPTION
------------ | -------------
docker run `image` | RUN A CONTAINER FROM AN IMAGE (By default the container is running in attach mode)
docker run `image`:`tag`	| RUN A CONTAINER FROM AN IMAGE FOR A SPECIFIC VERSION
docker run -d `image` | RUN A CONTAINER IN DETACH MODE (If you would like to attach back to the running container) <br/>docker attach `conatiner`
docker run -i `image`	| RUN CONTAINER IN INTERACTIVE MODE
docker run -it `image`	| RUN CONTAINER IN INTERACTIVE MODE AND ATTACHED TO TERMINAL


when you run the `docker run Ubuntu` command, it runs an instance of ubuntu image and exits immediately.
If you list all containers using `docker ps -a` including those that are stopped you will see that the new container you ran is in an exited stage.
Because containers are not meant to host an operating system.
Containers are meant to run a specific task or process, Once the task is complete the container exits a container only lives as long as the process inside it is alive.
If the web service inside the container is stopped or a crash then the container exits.
This is why when you run a container from an ubuntu image it stops immediately because Ubuntu is just an image of an operating system that is used as the base image for other applications. There is no process or application running in it by default.


## DOWNLOAD AN IMAGE BUT NOT RUNNING
```git
docker pull `image`
```


## GET RUNNING CONTAINER LIST
```git
docker ps
docker ps -a
docker ps | less -S
docker ps --format 'table {{ .ID }}\t{{.Image}}\t{{ .Names }}\t{{ .Ports }}' -s
docker ps --format 'table {{ .ID }}\t{{.Image}}\t{{ .Names }}\t{{ .Ports }}' | less -S
docker ps --format "{{json .}}" --no-trunc
docker ps --format "{{json .}}" --no-trunc | jq
```


## STOP A RUNNING CONATINER
```git
docker stop `conatiner`
```


## GET ALL IMAGES
```git
docker images
```


## REMOVE A STOPPED OR EXITED CONATINER
```git
docker rm `container` `container` `container`
```


## REMOVE AN IMAGE 
Must ensure no conatiner running off of that image, must stop and delete all container first
```git
docker rmi `image`
```


## ACCESS DOCKER CONSOLE TO EXECUTE A COMMAND
```git
docker exec -it CONTAINER_ID /bin/bash
docker exec -it CONTAINER_ID bash
docker exec -it CONTAINER_ID sh
```


## GET RUNNING PROCESSES IN THE CONTAINER
```git
docker exec -it `container` ps -eaf
```


## PORT MAPPING
Default IP of a docker container is `172.X.0.X`, this is internal IP and only accessible within docker host.
Default IP of the docker host is `192.168.1.X / host.docker.internal`, this IP is accessible outside the docker host.

MAP port to docker container
```git
-p 80:5000
```


## VOLUME MAPPING
```git
docker run -v /opt/datadir:/var/lib/mysql mysql
```

By running this command we are going to save mysql data in our local storage so that even if the container is remove we have the data.



## GET CONTAINER INFORMATION
```git
docker inspect `container`
```


## GET THE IP ADDRESS OF YOUR CONATINER
```git
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' `container_id`
docker inspect `container_id` | grep IPAddress
docker inspect `container_id` | jq .[0].NetworkSettings.Networks[].IPAddress
```


## GET LOGS OF A CONTAINER
```git
docker logs `container`
```


## HISTORY OF CONTAINER
```git
docker history `container`
```


## NETWORK RELATED COMMANDS
PATTERN | DESCRIPTION
------------ | -------------
docker network ls <br /> docker network ls \| less -S | LIST NETWORK
docker network rm ```network``` | REMOVE NETWORK
docker network inspect ```network``` | SHOW NETWORK INFORMATION
docker network connect ```network``` ```container``` | CONNECT A CONTAINER TO A NETWORK
docker network disconnect ```network``` ```container``` | DISCONNECT A CONTAINER TO A NETWORK
