---
layout:     post
title:      kubernetes Credentials and Secrets 
#date:       201-0-0 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    .
categories: kubernetes
thumbnail:  k8s
tags:
- k8s
- kubernetes
---

## Description:

In this post we are going to create a secret in k8s and give access to this one in a specific pod by 2 ways.

_Steps:_

    1) Create a Secret base64.
    2) Pod with access to the secret through a Volume.
    3) Pod with access to the secret trhough environment variables.

----

__1) Create a Secret base64.__

The reason to create the secret on base64 is a way of representing binary data using only printable (text) characters.

#### Create the secret
~~~
echo -n user | base64
dXNlcg==
echo -n password | base64
cGFzc3dvcmQ=
~~~

Now we have the user and password in base64 format, in the next step we are going to create this secret in k8s (kubernetes).

vim secret.yaml
~~~
apiVersion: v1
kind: Secret
metadata:
  name: test-secret
data:
  username: dXNlcg==
  password: cGFzc3dvcmQ=
~~~

~~~
kubectl create -f secret.yaml
~~~
---
__2) Pod with access to the secret through a Volume.__

vim pod-secret-volume.yaml
~~~
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secret-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
      volumeMounts:
          # name must match the volume name below
          - name: secret-volume
            mountPath: /etc/secret-volume
  # The secret data is exposed to Containers in the Pod through a Volume.
  volumes:
    - name: secret-volume
      secret:
        secretName: test-secret
~~~

now create the pod:
~~~
kubectl create -f pod-secret-volume.yaml
~~~

CHECK THE SECRETS INSIDE POD

~~~
kubectl exec -it nginx-secret-pod -- /bin/bash
root@nginx-secret-pod:/# cd /etc/secret-volume
root@nginx-secret-:/etc/secret-volume# cat username; echo; cat password; echo
~~~
The output is your username and your password:
~~~
user
password
~~~
---
__3) Pod with access to the secret trhough environment variables.__
vim pod-secret-envars.yaml
~~~
apiVersion: v1
kind: Pod
metadata:
  name: secret-envars-pod
spec:
  containers:
  - name: nginx-envars-container
    image: nginx
    env:
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          name: test-secret
          key: username
    - name: SECRET_PASSWORD
      valueFrom:
        secretKeyRef:
          name: test-secret
          key: password
~~~

Now create the pod:

~~~
kubectl create -f pod-secret-envars.yaml
~~~

CHECK THE SECRETS INSIDE POD

~~~
kubectl exec -it secret-envars-pod -- /bin/bash
root@secret-envars-pod:/# printenv
~~~

The output is your username and your password:

~~~
...
SECRET_USERNAME=user
...
SECRET_PASSWORD=password
~~~
