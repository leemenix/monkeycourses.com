### Why Docker?
Isolete your code in separate containers, build and manage this containers for you and share them among other machines. 
Docker is not Virtual Machine, it's rather share resorces of machine with help of cgroups and (tbd.).
### What is Container
Container is separated, organized part of (tbd.)
Conatiner main component are:
Code, Configs, Processes and Networking, all dependencies and share kernel of OS (tbd.). 
Talk over Virtual network with other containers on diferent OS's (RedHat, Debian, suse)
Container, as it is, have all of software that have everything needed to run a service (sealed self-contained)

### What Kernel Do
Respond to messages from the hardware
start schedule programs 
control and organize storage
pass message betwween prpograms
alolcate resources, memory , cpu, network , and so on

And docker is program which manage kernel
writen in Go
manage kernel features
	use cgroups to contain processes (group proccess togheter)
	use namespaces to contain network (split network stack , linux feature )
	use copy-on-write filesystems to build images (you have image , will not change but you can run your stuff on top of it)
Docker just help us to make scripting distributed system easy

doker is divided in two programs client and server
# what linux socket is?
talk to each other via socket (network (long distances) or a file (same machine or on virutal host or even within container /var/run/docker.sock (linux)))
find pictures there (tbd)
docker run -ti --rm -v /var/run/docker.sock:/var/run/docker.sock docker sh #(hook to control it's own server)
docker info
docker run -ti -rm ubuntu bash (start container from clinet withing a container; not docker in docker this is example how to controle server that controling a docker server out of that container from other docker client container)



### namespaces and networking
Docker manage networking for you
Netwokring layers from bottom:
	ethernet: moves frames on a wire (wifi)
	IP layer: move packet on a local network (world)
	Routing: forwards packateges between network
	Ports: address specific programs on a machine , listening for trafic(0-65527 (tbd))

Networking
Docker use bridge to create virtual network inside your machine and manage it for you; when crate new network it's create new bridge, analog to switch real, but just on virtual level and connecting containers on that virtual layer
Bridge contorl Ethernet layer, containers that acutly talk to each other

# demo will need brctl (brige control ctl)
# --net=host turn of isolation, just for demo and never use in prod, good for debuging
docker run --rm -ti --net=host ubuntu:16.04 bash 
apt-get update && apt-get install bridge-utils
brctl show

# new terminal
docker create network my-newnetwork

# back to first machine
brctl show # point to bridge instead of magic

Routing (how docker moves packages between layer and between internet)
use firewall rules to move packet between networks (build in firewall featruse of )
NAT (network address translation ) 
basicaly when packet goes out, change the source adress ont the way out, when packet comes back, change destination address to , look like packet comes back from original address

iptables -n -L -t nat # L list t table
docker run -ti --rm --net=host --privileged=true ubuntu bash
apt-get update && apt-get install iptables
iptables -n -L -t nat

# start new container
docker run -ti --rm -p 8080:8080 ubuntu bash

# go back in privilede ocntainer 
iptables -n -L -t nat
# so exposing ports are nothing else than port forwading rule on networking layer

# Namespaces
Linux feature that allow complete networki isolation to diferent processess to be attached to private network segmetns; this way it's dont allow to mess with  networking of other process  

processes from containers are atached to virtual network devices, and this virtual netwok devices are atached to bridges, whiche let them talk to any other container attached to same bridges; basicaly it's private netowrks ara bridge into a shared network with th rest of the containers

Containers have its "virutal network card"
containers get it's own copy of networking stack

### cgropus and process, 
explain from linux perspecitve

process come from other process - parent child relationship
when a child process exits, it returns an exit code to its parent
speical process is Process Zero; called init, the process that starts the rests and is grandpa to all

In docker container starts with an init process ant disapier wht that process eixits; split all other process below it

docker run -ti --rm hello-me ubuntu bash

# from host with docker inspect (extract all info you ever need about continer)
docker inspect --format '{{.State.Pid}}' hello-me
#copy output id process

# start super priviled container 
docker run -ti --rm --privileged=true --pid=host ubuntu bash
kill output_id # container on other side will exit

Docker - Resource limiting
scheduling cpu time 
memory allocation limits
inherited limitation and quotas (sum total can't be exceded)
can't escape limits

### storage in brief
layers of linux file system
actual storage device (kernel menage this)
logical groups (raid , ... partition)
file systems, which bits on the drive is part of witch file (kernel track this spot)
fuse filesystems (tbd)
the secret COW's (copy on write)
##moving cows
the contents of layers are moved between containers in gzip files
containers are independent of th storage engine
any container can be loaded almost anywhere
it's possible to run out of layers on some of the strage engines

# volumes and bind mounting
the linux vfs(virtual file system)
mounting devices on the vfs
mounting directories on the vfs

docker run -ti --rm --privileged=true ubuntu bash
mkdir somedir
cd somedir
mkdir newwork
cd newwork
touch a b c d f
cd ../../
mkdir other
cd other
touch o-a o-b o-c
cd ../
ls -R

mount -o bind other somedir
ls -R
df -h
umount other
ls -R

gettin the mount order correct
mounting volumes - always mountes hosts filesystem over the guest filesystem

### Docker components
client server services

### What you will learn?

### What you should know?
Basic comand line movements
Linux basics
Basic networking knowledge

### Some Linux basics
for in
ls
cd
autocomplete
. (dot represent current directory)
sourcing files
nc comand

### Docker Hub

### Docker installation 
Linux installation; specific version installation

sudo groupadd docker
sudo usermod -aG docker $USER


### Docker on Windows 
Docker Desktop (boot2docker obsolete)

### Docker on Mac
Docker Desktop (boot2docker obsolete)

### Docker Hello world
docker run hello-world (from docker)
#docker basic help with help command (tbd.)

### Docker flow 
Images
Container

## stateful vs stateless
tbd.

#explain headers in comands output (tbd.)
## from images to container
image -> docker run -> running docker container
docker images
docker run (from images)
docker run -ti ubuntu:latest bash (cat /etc/lsb-release)
docker ps -a
dockre ps --format $FORMAT ($FORMAT tbd. use some other variable!)
#docker run container with name

## container to images
image -> docker run -> running docker container -> stop container -> docker commit -> new image
docker ps -l 
docker ps -a
crate container 
docker run -ti ubuntu:latest bash
echo "some infomataion" >> somefile
docker commit id-of container
docker tag tag name of long id (no need for tag, just intermediate step to see how it's working)
docker commit newimage of previous image

### Running code in docker
docker have main process
container stop when process stop
docker run --rm --ti ubuntu:latest sleep 5s (run and delete after you done)
docker run --ti ubuntu bash -c "sleep 3; echo good job"
docker run -d --ti ubuntu bash (detached)
docker attach name_of_container (CTRL + p, CTRL + q)
# explain running with tags, latest and so on (tbd.)

docker exec (start another process in existing container, mainly for debuging)
docker exec --ti name_of_container bash (CTRL+d)

### manage containers , debug , logging
docker logs 
docker logs container_name
docker run --name docker_c_name -d ubuntu bash -c "bad_command /etc/password"
docker logs docker_c_name

## stop and remove
docker run --ti ubuntu bash
docker kill container_name
docker ps -l (last container exit)
docker rm old_container 
#for i in `docker images | grep -v IMA | awk {print $4}`...

## memory limits
docker run --memory maximum-allowed-memory image-name command
docker run --cpu-shares (relative to ther conatiner)
docker run --cpu-quota (hard limit)


## best practices , lesson learned 
dont fetch dependencies during startup, someone will remove repo in future, have it all ready built in in your image
never leave important stuff on stoped conatiner

### port exposing
conecting containers 
private network creating
expose port (expose internal to external ports)
docker run -p external-port (host machine):internal-port (within docker container)
# docker -p (publish - check this)
docker run --rm -p 80:80 -p 8080:8080 --name echo-server ubuntu:14.04 bash
# from containernetcat (-lp port listen) in container
nc -lp 80 | nc -lp 8080

# from host machine
nc localhost 80
write something
# from host machine paralel run 
nc localhost 8080

# or from witihn new container that have nc on it
docker run --rm --ti ubuntu:14.04 bash
nc host.docker.internal 80
# other new container 
docker run --rm --ti ubuntu:14.04
nc host.docker.internal 8080

# exposing ports dynamically
docker run --rm -ti -p 80 -p 8080 --name mycontainer ubuntu:14.04 bash
nc -lp 80 | nc -lp 8080

# first terminal
docker port mycontainer
nc localhost (dynamical port)

# other terminal
nc localhost (dynamical port)
# what docker run -rm -ti -p --name container ubuntu bash expose? (tbd)

## udp network protocol as well
docker run -p 80:80/udp -ti ubuntu:14.04
docker run --rm -ti 80/udp --name newserver ubuntu:14.04 bash
docker port newserver
nc -ulp (dynamic port)
write something

### Docker Networking (Container networking)

client Container -> virtualNetwork -> Host Network -> server container
client Conntainer -> server container
docker network ls
# explain networks (public, host,  none, bridge)
docker network create my-new-network
docker run --rm -ti --net my-new-network --name goku-server ubuntu:14.04 bash

#  from conatianer
ping goku-server
pint krilin-server
docker run --rm -ti --net my-new-network --name krilin-server ubuntu:14.04 bash
# from both
ping goku-server
pint krilin-server
# on krlin 
nc -lp 80
# fomr goku
nc krlin-server 80
write something from both side

# more then one network
docker network create goku-private
docker network connect goku-private goku-server
docker run --rm -ti --net goku-private --name chi_chi-server ubuntu:14.04 bash

# from chi_chi-server
ping goku-server
ping krilin-server
# from krilin-server
ping goku-server
ping chi_chi-server
# from goku-server
ping krlin-server
ping chi_chi-server

### Legacy linking (should avoid)
Links all ports, in one direction from source to destination, envirionment variables will be shared, depends on starup order, restart can break

docker run --rm -ti -e PASS=my-pass --name goku-server ubuntu:14.04 bash
docker run --rm -ti --link goku-server --name krilin-server ubuntu:14.04 bash

#on goku-server
nc -lp 80
#on krlin-server
nc goku-server 80
write somehitni
## try other direction , should not work as linking work in one direction

#on goku-server
env
echo $PASS
#on krlin-server
env
echo $PASS (or whatever env give as as output)

### work with images
docker images
#image tagging
#docker commit tags for you
docker ps -l --format=$FORMAT
docker images
docker commit id_of_container name-of-container (will be latest tag)
docker commit id_of_container name-of-container:tag
#registry.com:port/organization/image-name:tag-version
#organization/image-name:tag-version is ok as well
docker pull (automaticaly trigered by doker run)
docker push
docker rmi image
docker rmi image:tag

### volumes - share data , virtul disks, shared folders 
# not part of images

# persisten 

# ephemeral

mkdir share
docker run -ti -v /full_path/share:shared-dir-in-container ubuntu bash
ls /shared-dir-in-container
touch /shared-dir-in-container/touchofdestiny
exit
ls share
# share file in same, be secure that file exist otherwise docker will think it's directory and will share as folder
# (tbd) check if docker share not existing source/destination dir

## share between containers volume-from
#start new container
docker run -ti -v /shared-dir-in-container ubuntu bash
echo some >> /shared-dir-in-container/somedata
# start new container
docker ps # to get id from previous
docer run -ti --volumes-from id_of_last ubuntu bash
ls /shared-dir-in-container
echo newstuf >> /shared-dir-in-container/newdata
# check this out (tbd)
docker run -v volume-name ubuntu bash 
autor say (The command docker run -v volume-name #ubuntu bash creates a volume that is _____, and is deleted when _____.The command docker run -v volume-name ubuntu bash creates a volume that is #_____, and is deleted when _____.)
You are correct!
ephemeral; no container is using it)
# end check this out (tbd)

# exit first conatinaer
# start new container
docker ps
docker run -ti --volume-from id-of-second-container ubuntu bash
# inherited from first, and than inherited from second

### docker registries
#manage and distribute registries
#docker hub
#private registries
docker search ubuntu
# explain docker hub (maybe before)
docker login
u:
p:

docker pull debian:sid
docker tag debian:sid leemenix/image:v01
docker push leemenix/image:v01 (in the wild)

#dont push images contain pass ever!
# clean up on regular basis
# dont trust until you check what it's about

### build docker images with Dockerfile
recipe/program/script to create local image
docker build -t name-of-image . 
#dot represent current directory, oterwise you need tell docker where your Dockerfile realy is by defining full path, this will be on your local image registry
# each line/step create new image, previous image is unchanged
# image will be huge if files are large
one Dockerfile look like
FROM ubuntu
MAINTAINER "leemenix@monkeycourses.com"

LABEL Description "image descript"
RUN apt-get update

# each step is cached! show them
# hint/tip Put parts that you change at the end of Dockerfile
# not shell scripts (because easier for learning)
# process need to be in one line if you want to be running, each line is separate call to docker run
# Environment you set will be set on the next line (with ENV)

Dockerfile
FROM alpine-linux # will use small image
RUN echo "Goku's simple image." # during build 
CMD echo "Hello from Goku's container." # when container based on image is started
# check diference CMD and RUN

mkdir goku
cd goku
vim Dockerfile
...
docker build -t goku .
# explain steps here
docker images (check tag name)
docker run --rm goku 

# vim Dockerfile
FROM debian:sid
RUN apt-get -y update
RUN apt-get install nc vim
CMD ["/bin/vim", "/tmp/file"]
docker build -t krlin:v1 .
docker images
docker run --rm -ti krilin:v1 (write some and save)

# vim Dockerfile
FROM krilin:v1
ADD file /tmp/file
CMD ["/bin/vim", "/tmp/file"]
# echo "I was here. Goku :)" >> file
docker build -t krilin:v2 .
docker run --rm -ti krilin:v2 (add some text)

# Dockerfile syntax
FROM always first; can be multiple 
MAINTERNER who maintain
RUN run command throth shell; 
RUN tar xvf somearchive.tar.gz /opt
ADD run.sh /run.sh; 
ADD somearchive.tar.gz /opt (will uncomper)
ADD url.pdf /opt (from url)
ENV SOME_VAR=value # valid during build and container runtime
# explain ENTRYPOINT vs CMD
ENTRYPOINT ls #(ls -> all will be argument to ls, more like begging of command ); add to
CMD ["/usr/bin/ls"] #whole command to run (if run something else after running container command, that somehting else will be run instead of CMD command); replace
#you can combine ENTRYPOINT and CMD
# explain shell vs exec form
vim /tmp/file
["/bin/vim","/tmp/file"] # run vim directly without sourounding with bash
EXPOSE 8080 # maps port in container
VOLUME ["/host/data" "/container/data"]
VOLUME ["/host/share"] # create volume that other container can use (can be inherited by other containers); avoid share in Dockerfile
WORKDIR /opt # like using cd; set working dir for build phase as well as docker runtime; The Docker file WORKDIR command changes directories both for the rest of the Docker file, and in the finished image
USER leemenix # run command as user; in case you need specific user of id like for nfs sharing
USER 1001 

### Multi Docker file
# make files small and complete 
# multistage builds (recent feature from Docker)

Dockerfile (make diferent example)
FROM ubuntu:14.04
RUN apt-get -y update
RUN apt-get -y curl
RUN curl https://google.com | wc -c > google-size
ENTRYPOINT echo google is this big; cat google-size 

docker build -t gooku .
docker run --rm gooku 
docker images #(how big image is; will see that is preaty much big for just outputing text on screen)

Dockerfile
FROM ubuntu:14.04 as builder
RUN apt-get -y update
RUN apt-get -y curl
RUN curl https://google.com | wc -c > google-size

FROM alpine
COPY --from=builder /google-size /google-size
ENTRYPOINT echo google is this big; cat google-size 

docker build -t goooku .
docker -rm goooku
docker images

# avoid golden images
include installer in project
have canonical build that build evertyhing from scratch
tag your builds with the git has 
use small base images , alpine
build images you share from Dockerfile, always
don't ever let pass out there in any layer


### Registries in details
is a program 
stores layer and images
listen on port 5000 by default
maintan and index and searches tags
authorizes and authenticates connections 

the offical python docker registry
nexus

docker makes installing netwrok 
the rregistry is network/docker service 

docker run -d -p 5000:5000 --restart=always --name registry registry:2

docker tag ubuntu:14.04 localhost:5000/myregistyr/my-ubuntu:99 (tbd. change names)
# check docker offical for docker registry before you put in production
elastic container registry aws
google cloud container registry
azure container registry

#savin and loading container localy
docker save 
docker load

docker save -o my-image.tar.gz debian:sid busybox ubuntu:14.04
docker rmi all
docker load -i my-image.tar.gz
migrate between storage types 
shiping images on disk 

### orchestration
many orchestratin system for docker
main philosophy of orchestraion is:
  start containers--and restart them if they fail (keep them running)
  service discovery -- allow them to find each other
  resource allocation - matche conttainers to computer

# Docker compose 
single machine coorignation
designed for tesitng and devlelopment, not for large systems
what it does brings up allyour containers volumes n, networks etc with one command
# Kubernetes
containers run programs
pods - group containers toghether (like docker compose) but do dynamicely distributed, service discovery, 
service make pods availble to others
labels are used for very advanced service discovery
make scritpting large operation possible with the kubectl command
very flexible overlay netrowking 
runs equlay on your own hardware or a cloud provider
built-n service discovery

# ec2 container service (ECS)
task definistions 
  define a set of containers that aleways run toghetre (analogy to pods)
tasks 
  aclutaly makes a container run right now
services and and wxpose it to he net
  ensures that a task is running all the time

ties into aws eco system
connects load balancers (elbs)to services
can cretar oyu r own host instance in aws
make yuor instance start the agent and join the cluster
pass the docker contorl socket into agent

provide docker repos and its easy runt your own repo
note continers tasks can be part of CF stacks

# aws fargate (as service)
# swarm docker
# kubernets engine
# azure kubernetes servise (AKS)

### Kubernets in AWS practicaly
Create cluster
kubectl get services
# replication controler and service
kubeclt apply -f (from offical AWS page redis maseter service)

...
kubectl get services
kubeclt get services -o wide
# paste dns name and port in browser
kubectl delete r/redis-master ...
kubectl get services

# Google kubernest engine
create new cluster
#google  cloud shell
gcloud config set project id_ofproject
gcloud config set zone us-west1-a
gcloud container cluster create new-cluster
kubectl expose deployment hello-server --type LoadBalncer \
--port 80 --target-port 8080
kubectl delete service hello-server







### extra
# explain portainer 
