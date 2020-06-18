.. title: docker on 32bit system  (windows 7 on core2duo)
.. slug: docker-on-32bit-system-windows-7-on-core2duo
.. date: 2014/10/19 18:43:12
.. tags: 
.. link: 
.. description: 
.. type: text

Docker is very usefull tool for fast shipping application with its dependicies. 
It's totally different from Vagrant which I used before .
Using them both may be very powerfull tool.
So I started to play with docker but I had problem - my everyday environment is Debian but at my work
I got 32bit core 2 duo workstation with windows7 installed (and no possibility to work on my laptop - security).
Fortunatelly this processor has VT-x support so I could run 64bits virtual machines.
I wanted to install boot to docker but unfortunatelly installation and boot2docker.exe are for 64 bits only so I just
used their iso image because it was very lightweigh.
First of all I had to convert this "live cd" image to vmdk I used this one

http://static.dockerfiles.io/boot2docker-v1.2.0-virtualbox-guest-additions-v4.3.14.iso
qemu-img convert -f raw -O vmdk Nowy\ folder/boot2docker.iso boot2docker-disk.vmdk

Now we have ready boot2docker disk out of live image.
Start virtualbox, create new virtualmachine, 
select linux 64bits, 
and when there will be step with disk choose Existing disk option and select newly created boot2docker-disk.vmdk

after all I could start to create new virtualbox machine and select vmdk disk to existing one boot2docker-disk.vmdk
we have ready and working virtual boot2docker machine .

