---
layout:     post
title: 		Docker-Machine with kvm driver.
#date:       2015-09-06 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    Deploy a vm using docker-machine with kvm driver.
categories: kvm
thumbnail:  kvm
tags:
- kvm
---

## Install Docker-Machine:
~~~
curl -L https://github.com/docker/machine/releases/download/v0.15.0/docker-machine-$(uname -s)-$(uname -m)
chmod +x /tmp/docker-machine &&
sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
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
