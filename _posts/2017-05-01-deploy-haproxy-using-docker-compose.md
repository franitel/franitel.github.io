---
layout:     post
title: 		HAPROXY using docker-compose.
#date:       2015-09-06 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    Deploying HAproxy using docker-compose 
categories: loadbalancer
thumbnail: haproxy
series: LoadBalancers
tags:
- haproxy
- loadbalancing
---
In this second post about LoadBalancers we are going to deploy an haproxy in the easy way.

### CONTENTS.
* Introduction
* Environment
* Deploy haproxy

---

### Introduction
To deploy our haproxy we are going to use docker-compose.
If you need to install docker compose you can do:
~~~
apt-get install python-pip
pip install docker-compose
~~~

### Environment
For this example we have 5 servers:
1 HAproxy server.
4 nginx servers.

The HAproxy is listening on port 80, when we recei

### Deploy haproxy with 4 nginx servers
1) Clone my git repository with all files
~~~
git clone https://github.com/franitel/load-balancer.git
~~~
2) Go to haproxy folder
~~~
cd haproxy
~~~
Now you can see some files, for now only we need to see the [docker-compose.yml](https://raw.githubusercontent.com/franitel/load-balancer/master/haproxy/docker-compose.yml) file
~~~
haproxy:
    image: haproxy:1.6
    volumes:
        - ./haproxy:/haproxy-override
        - ./haproxy/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro
    links:
        - nginx1
        - nginx2
        - nginx3
        - nginx4
    ports:
        - "80:80"
        - "70:70"

nginx1:
    build: ./web
    expose:
        - 80
nginx2:
    build: ./web
    expose:
        - 80
nginx3:
    build: ./web
    expose:
        - 80
nginx4:
    build: ./web
    expose:
        - 80
~~~
Here we can see all our environment
3) Run the docker-compose command
~~~
docker-compose up -d
~~~    
4) In http://localhost:70 port you can see the haproxy statistics

	user:	  user
	password: pass


5) To check that all is working fine you can do:
~~~
docker-compose ps
~~~
6) Go to http://localhost

Now you can refrest the website (F5) and on each refresch you will see the container hostname in the website, in my case:

* e40c136075cd
* d282ca09a79e
* c9e45c1bb0c3
* 41442643aefd

7) Here you have the haproxy.cfg file
~~~

global
  log 127.0.0.1 local0
  log 127.0.0.1 local1 notice
  maxconn 4096

defaults
  log global
  mode http
  option httplog
  option dontlognull
  timeout connect 5000ms
  timeout client 50000ms
  timeout server 50000ms

listen stats
  bind 0.0.0.0:70
  mode http
  stats enable
  stats hide-version
  stats scope .
  stats realm Haproxy\ Statistics
  stats uri /
  stats auth user:pass

frontend balancer
  bind 0.0.0.0:80
  mode http
  default_backend web_backends

backend web_backends
  mode http
  option forwardfor
  balance roundrobin
  server nginx1 nginx1:80 check
  server nginx2 nginx2:80 check
  server nginx3 nginx3:80 check
  server nginx4 nginx4:80 check
  option httpchk GET /
  http-check expect status 200

~~~
