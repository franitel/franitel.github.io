---
layout:     post
title:      Basic kubernetes setup. 
date:       2016-02-10 13:03:11
author:     Francisco José Rodríguez Rubio
summary:    Installing k8s using kubeadm
categories: k8s
thumbnail:  k8s
tags:
- k8s
---
Today I'm going to do a Basic kubernetes cluster.

## REQUIREMENTS

	* 2 GB or more of RAM per machine.
	* 2 CPUs or more on the master
	* Full network connectivity between all servers.

## Install **kubeadm** on all servers (Master and nodes). (ubuntu,debian)
~~~
apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
reboot
~~~

## Inicializing the master
~~~
kubeadm init --pod-network-cidr 10.244.0.0/16
~~~

## Passthrough the credential to kubectl
~~~
export KUBECONFIG=/etc/kubernetes/admin.conf
~~~

## Installing a pod network add-on
~~~
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
~~~

## Join nodes to the cluster
   When you launched the kubeadm init on master you get the following command:
   Launch in all nodes:
~~~
kubeadm join --token=bb12ca.e5bbbedfa0c58788 192.168.0.34
~~~
Change the token and the ip-master by yours.
If you forgot get the token you can get again using the following command in the master:
~~~
kubeadm token list

~~~
## Check all Nodes are joinned:
~~~
kubectl get nodes
~~~

## Check that all pods are working without errors: 
~~~
kubectl get pods --all-namespaces
~~~
~~~
NAMESPACE   NAME   READY   STATUS    RESTARTS   AGE

ingress-nginx-kubernetes-worker   default-http-backend-kubernetes-worker-7f7f76df64-2kvfc           1/1     Running   17         29d
ingress-nginx-kubernetes-worker   nginx-ingress-controller-kubernetes-worker-s9g44                  1/1     Running   17         29d
kube-system                       heapster-v1.6.0-beta.1-865845cbc6-v4xnk                           4/4     Running   68         29d
kube-system                       kube-dns-8f7866879-lz8x5                                          3/3     Running   52         29d
kube-system                       kubernetes-dashboard-654cfb4879-r9lms                             1/1     Running   17         29d
kube-system                       metrics-server-v0.3.1-54b884db75-nfdcp                            2/2     Running   34         29d
kube-system                       monitoring-influxdb-grafana-v4-5866497777-4pq96                   2/2     Running   34         29d
kube-system                       tiller-deploy-85686555b8-67bvq                                    1/1     Running   1          13d

~~~

