---
layout:     post
title:      Deploy Docker Swarm cluster multi-master. 
#date:       201-0-0 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    Deploy Docker Swarm cluster in multi-master nodes.
categories: docker
thumbnail:  swarm
tags:
- docker
- swarm
---

### Description
In this post I'm going to setup a docker swarm cluster using 3 nodes where all nodes are master.

#### System Resources
we need 3 centos 7 with the following configuration:
~~~
swarm1  -->  172.26.12.1
swarm2  -->  172.26.12.2
swarm3  -->  172.26.12.3
~~~
#### Install docker-ce in all servers

~~~
swarmX#> sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
swarmX#> sudo yum install -y yum-utils device-mapper-persistent-data lvm2
swarmX#> sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
swarmX#> sudo yum install docker-ce
~~~

#### Initialize the cluster in swarm1
~~~
swarm1#>   docker swarm init --advertise-addr 172.26.12.1
~~~

#### Show the tokens

worker token
~~~
swarm1#>  docker swarm join-token worker
~~~

master token
~~~
swarm1#>  docker swarm join-token manager
~~~

#### Join nodes using the master token. ( Run in swarm2 and swarm3)
~~~
swarm2#> docker swarm join --token SWMTKN-1-4pipgzprgeee73d8h3hwwakp3dx6aes6dsgqs

swarm3#> docker swarm join --token SWMTKN-1-4pipgzprgeee73d8h3hwwakp3dx6aes6dsgqs
~~~

#### check nodes in the cluster
~~~
swarm1#> docker node ls
~~~

#### Deploy a service in cluster swarm
~~~
swarm1#> docker service create --replicas 3 -p 80:80 --name web1 serviceName nginx
~~~

#### Check the service

~~~
swarm1#> docker service ls
swarm1#> docker service ps web1
~~~


