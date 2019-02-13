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
* Environment.
* Install haproxy.
* Configure haproxy.cfg

---

### Introducction.
#### Definition:
As the haproxy website said:   __haproxy__ is an Open Source Software to do Load Balancer
and Application Delivery Controller.
What does this mean?, basically you can use haproxy to route traffic regardless of the protocol,
for example it could provide load balancing to:

* __Webservers:__ Such as a number of hosts running Apache, nginx, lighttped, etc.
* __Mail servers:__ Such as a small pool of hosts running postfix, exim4, qpsmtpd, etc.
* __Arbitrary TCP services:__ Such as APIs implemented in go, lua, or node.js.

#### Load-Balanching __MODES__

There are various different modes, which may be specified via the "balance" directive, in the backend section. 
The three most common approaches are:

* __balance roundrobin:__ Distributing each request in turn to the next server.
* __balance leastconn:__ Distributing each incoming request to the least loaded backed we have.
* __balance source:__	Distribute each request to a particular server, based upon the hash of the source IP making that request.


The most popular use-case though would be directing traffic to webservers. 
In this next example I'll show connections made to a single IP address can be passed to four backend hosts.

### Environment
The Environment will consist of 2 parts, the FrontEnd and the BackEnd.

The FrontEnd is a machine with haproxy listening on 80 port :
* haproxy port 80

And The BackEnd are 4 nginx server listening on 8081 8082 8083 8084:
* nginx1  port 8081
* nginx2  port 8082
* nginx3  port 8083
* nginx4  port 8084


### Install haproxy.
In this case we are going to do the setup in Ubuntu:bionic:
~~~
sudo apt-get update
sudo apt-get install haproxy
~~~


### Configure haproxy.cfg

sudo vim /etc/haproxy/haproxy.cfg
~~~
global
        log     /dev/log    local0
        log     /dev/log    local1 notice
        chroot  /var/lib/haproxy
        user    haproxy
        group   haproxy

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull

#
#  Listen on *:80 - Send traffic to the backend named "nginx"
#
frontend www-http
    bind *:80
    default_backend nginx

#
# Back-end definition.
#
backend nginx
    mode http
    balance roundrobin
	stick-table type ip size 2000k expire 30m
	stick on src
    server nginx1 10.0.0.10:8081 check
    server nginx2 10.0.0.20:8082 check
    server nginx3 10.0.0.30:8083 check
    server nginx4 10.0.0.40:8084 check
~~~


In the backend we are specified to use mode http with balance roundrobin, also we have stick-table,
this is keeping the session between the client and the final server.
	

In the next post I'll deploy an haproxy using docker-compose.yml

