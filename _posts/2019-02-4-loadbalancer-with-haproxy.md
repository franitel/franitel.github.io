---
layout:     post
title: 		Load Balancing with haproxy.
#date:       2015-09-06 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    Basic example deploying haproxy.
categories: loadbalancer
thumbnail: haproxy
series: LoadBalancers
tags:
- haproxy
- loadbalancing
---
Today I'm going to start a series of LoadBalancers posts.
In this first post I'm going to talk about HAPROXY.
### CONTENTS.
* Introduction about HAPROXY
* How to install in a centos server
* Config for a basic deployment. (Load Balancer Round-robin)

---

### Introducction.
#### Definition.
As the haproxy website said:   __haproxy__ is an Open Source Software to do Load Balancer
and Application Delivery Controller.
What does this mean?, basically you can use haproxy to route traffic regardless of the protocol,
for example it could provide load balancing to:

* __Webservers:__ Such as a number of hosts running Apache, nginx, lighttped, etc.
* __Mail servers:__ Such as a small pool of hosts running postfix, exim4, qpsmtpd, etc.
* __Arbitrary TCP services:__ Such as APIs implemented in go, lua, or node.js.

#### Load-Balanching __MODES__

If for example we 

* __balance roundrobin:__ Distributing each request in turn to the next server:
* __balance leastconn:__ Distributing each incoming request to the least loaded backed we have:
* __balance source:__	Distribute each request to a particular server, based upon the hash of the source IP making that request:



I would like to do some posts about HAPROXY, because we have a lot of options and it is a very interesting tool.
In this post I'm going to configure HAPROXY



### Environment
In this case I'm going to configure HAPROXY to route http traffic. 

To do that I'm going to create 4 docker nginx containers in the followings ports:

haproxy port 8080

nginx1  port 8081
nginx2  port 8082
nginx3  port 8083
nginx4  port 8084

mariadb port 3306




###  haproxy.cfg

~~~
global
        log     /dev/log    local0
        log     /dev/log    local1 notice
        chroot  /var/lib/haproxy
        user    haproxy
        group   haproxy
        maxconn 1024
        daemon

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull

#
#  Listen on *:8080 - Send traffic to the backend named "nginx"
#
frontend www-http
    bind *:8080
    default_backend nginx

#
# Back-end definition.
#
backend nginx
    mode http
    balance roundrobin
	stick-table type ip size 2000k expire 30m
	stick on src
	stick-table type string size 30k expire 30m

    stick on src table https
    cookie SRV insert indirect nocache
    server nginx1 10.0.0.10:8081
    server nginx2 10.0.0.20:8082
    server nginx3 10.0.0.30:8083
    server nginx4 10.0.0.40:8084
~~~



HAproxy Sticky sessions
Health Checkup
	



### Docker-compose to test in a easily

vim docker-compose.yml

docker-compose up -d

I'm going to generate 4 connecting in a single IP address can be passed to four backend hosts.

To do this I'm going to create the following environment:

1 haproxy-server
1 server with wordpress
1 server with nginx


### Create DockerFile
~~~
FROM haproxy:1.7
COPY haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg
~~~

### Build the container
    docker build -t my-haproxy .



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
