.. title: docker
.. slug: docker
.. date: 2014-10-11 09:27:28 UTC
.. tags: docker, virtualization
.. link: 
.. description: 
.. type: text



First download download pre build image :
=========================================
sudo docker pull ubuntu

run pseudo tty:
===============
sudo docker run -t -i ubuntu /bin/bash

get image of running docker:
============================
sudo docker ps

output:
➜  ~  sudo docker ps               
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
c4fbc80d8926        ubuntu:14.04        /bin/bash           3 minutes ago       Up 3 minutes                            berserk_nobel       
➜  ~  
save container as your image:
=============================
sudo docker commit c4fbc80d8926 myimage_name

Running our image:
==================
sudo docker run -t -i myimage_name /bin/bash

Deploying your app and dependicies:
===================================
watch out to save container before you quit bash inside docker instance
find container id and save


Diff image container:
=====================
sudo docker diff 

Start docker container with name:
=================================
sudo docker run -i -t --name="IWAPPS2" iwapps /bin/bash           

Commit with name :
==================


sudo docker commit IWAPPS2 local -a "greg" -m "First image commit"
    
Actually, images are stored within /var/lib/docker/aufs/diff



 OUP  sudo docker ps                                                                   
 CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS
 NAMES
 95585eb7ea3c        cc68531a3552        /bin/bash           About a minute ago   Up About a minute
 mad_ptolemy         
 ➜  OUP  sudo docker commit 95585eb7ea3c iwps2 -a "greg" -m "First image commit"            
 c86eeb858bedf779105f759ca62d10ea59e01884e985e447d9ddf7f65ee32e3c
 ➜  OUP  sudo docker images                                                                              
 REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
 iwps2               latest              c86eeb858bed        5 seconds ago       617.5 MB
 local               latest              539a99ee88ee        22 minutes ago      348.5 MB
 <none>              <none>              d8f374aecae4        2 hours ago         348.1 MB
 iwapps              latest              85e0ae955062        4 hours ago         348.1 MB
 ubuntu              utopic              b8d67033ed07        23 hours ago        286.6 MB
 ubuntu              14.10               b8d67033ed07        23 hours ago        286.6 MB
 ubuntu              trusty              1357f421be38        23 hours ago        192.7 MB
 ubuntu              latest              1357f421be38        23 hours ago        192.7 MB
 ubuntu              14.04               1357f421be38        23 hours ago        192.7 MB
 ubuntu              14.04.1             1357f421be38        23 hours ago        192.7 MB
 ubuntu              12.04               2bed76595591        23 hours ago        120.2 MB
 ubuntu              12.04.5             2bed76595591        23 hours ago        120.2 MB
 ubuntu              precise             2bed76595591        23 hours ago        120.2 MB
 <none>              <none>              96864a7d2df3        3 weeks ago         205.1 MB
 ubuntu              12.10               c5881f11ded9        3 months ago        172.2 MB
 ubuntu              quantal             c5881f11ded9        3 months ago        172.2 MB
 ubuntu              raring              463ff6be4238        3 months ago        169.4 MB
 ubuntu              13.04               463ff6be4238        3 months ago        169.4 MB
 ubuntu              saucy               195eb90b5349        3 months ago        184.7 MB
 ubuntu              13.10               195eb90b5349        3 months ago        184.7 MB
 ubun

pt
The images are stored in /var/lib/docker/graph/<id>/layer.

Note that images are just diffs from the parent image. The parent ID is stored with the image's metadata /var/lib/docker/graph/<id>/json.

When you docker run an image. AUFS will 'merge' all layers into one usable file system.

docker inspect
docker images --no-trunc=true

Running an interactive shell
sudo docker run -i -t ubuntu /bin/bash


sudo baobab /var/lib/docker

docker pull ubuntu
docker run -i -t ubuntu:14.04 /bin/bash
sudo docker info
sudo docker version
sudo docker search ubuntu
sudo docker pull ubuntu
sudo docker images

