---
layout:     post
title:      kubernetes Basic deployment (namespace and resource limitation) 
#date:       201-0-0 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    Deploy nginx on kubernetes.
categories: kubernetes
thumbnail:  ngnix
tags:
- k8s
- nginx
- namespace
---

In this first deploy I'm going to deploy a simple nginx server, but I'm going to use namespace and resource limitation.

### Definitions:

	1) NAMESPACES: 
       Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces. Namespaces are intended for use in environments with many users spread across multiple teams, or projects.
    
	2) MEMORY: "256Mi": 
       256Mi is equal to 256 MegaBytes.

    3) CPU: "1000m": 
       One thousand MilliCPUs is equal to 1 CPU, you can use 500m or "0,5" to specify 1/2 CPU

### Kubernetes Example Deployment

In this basic example I'm going to do the following steps:
	1) Create a namespace
	2) Create a Nginx Deployment
	3) Create a Nginx Service
	4) Expose and access the Nginx Service

## Clone my respository

## Create a namespace ()
vim namespace-demo.yaml
~~~
apiVersion: v1
kind: Namespace
metadata:
  name: namespace-demo
  labels:
    apps: web-based
  annotations:
    type: demo
~~~
~~~
kubectl create -f namespace-demo.yaml
~~~

## Assign Resource Quota To Namespace

vim quota-namespace-demo.yaml 
~~~
apiVersion: v1
kind: ResourceQuota
metadata:
  name: cpu-mem-quota
  namespace: namespace-demo
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
~~~
~~~
kubectl create -f quota-namespace-demo.yaml
~~~

## Create A Deployment in namespace-demo
vim deployment.yaml

~~~
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
  namespace: namespace-demo
  annotations:
    monitoring: "true"
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources:
          limits:
            memory: "2Gi"
            cpu: "1000m"
          requests: 
            memory: "1Gi"
            cpu: "500m"
~~~
~~~
kubectl create -f deployment.yaml
~~~
Check the deployment
~~~
kubectl get deployments -n namespace-demo
~~~


## Create A Service And Expose The Deployment

vim service-demo.yaml

~~~
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: namespace-demo
spec:
  ports:
  - nodePort: 31111
    port: 80	
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  type: NodePort
~~~
~~~
kubectl create -f service-demo.yaml
~~~
Now you can see the service created running the following command:

~~~
kubectl get services  -n namespace-demo
~~~

## Connect to the nginx service 
To connect 
http://[kubernete-server-ip]:31111


## 
~~~

~~~

