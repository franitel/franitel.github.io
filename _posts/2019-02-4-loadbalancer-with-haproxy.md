---
layout:     post
title: 		Load Balancing with haproxy.
#date:       2015-09-06 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    Basic example deploying haproxy.
categories: loadbalancer
thumbnail:  haproxy
tags:
- haproxy
- loadbalancing
---
In this case

## Install Docker-Machine:
~~~
~~~
## Install kvm driver:
~~~
sudo curl -L https://github.com/dhiltgen/docker-machine-kvm/releases/download/v0.7.0/docker-machine-driver -o /usr/local/bin/docker-machine-driver-kvm
sudo chmod +x /usr/local/bin/docker-machine-driver-kvm
~~~

## Create VM:
~~~
docker-machine create -d kvm dm-swarm1
docker-machine create -d kvm dm-swarm2
~~~
## Connect to VM
~~~
docker-machine ssh dm-swarm1
~~~
