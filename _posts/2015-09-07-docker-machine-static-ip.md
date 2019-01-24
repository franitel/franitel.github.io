---
layout:     post
title:      Docker-Machine Static IP Address.
#date:       2015-09-06 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    Modify vm generated in docker-machine to have static ip address.
categories: kvm
thumbnail:  kvm
tags:
- kvm
- docker
- docker-machine
---

In this case we are going to modify the bootlocal.sh script to use static ip addres in vm generated using docker-machine.


## Static IP Address:

Connect to the server and edit the bootlocal.sh file:
~~~ 
docker-machine ssh swarm-master1
vi /var/lib/boot2docker/bootlocal.sh
~~~
Modify the bootlocal.sh script:
~~~
#!/bin/sh
kill cat /var/run/udhcpc.eth0.pid
ifconfig eth2 172.26.100.101 netmask 255.255.255.0 up
kill cat /var/run/udhcpc.eth1.pid
ifconfig eth0 192.168.17.101 netmask 255.255.255.0 up
~~~
