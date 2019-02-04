---
layout:     post
title:      Openshift pipeline Jenkins 
#date:       201-0-0 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    .
categories: openshift
thumbnail:  openshift
tags:
- openshift
- jenkins
---
In this tutorial we are going to create a jenkins server in openshift and we are going to create a pipeline to build a Docker container using a Dockerfile that is in my franitel.github.com repository.


### Steps

1) Create project

    oc new-project pipeline-project
        
2) Give permissions

    oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n pipeline-project

3) Deploy a Jenkins app

4) create the pipeline.

5) build the project.
    



## Install Docker-Machine:
~~~
curl -L https://github.com/~machine-$(uname -s)-$(uname -m)
chmod +x /tmp/docker-machine &&
sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
~~~

https://www.youtube.com/watch?v=07-Xx73y3zA

https://github.com/franitel/openshift/tree/master/pipeline-example
