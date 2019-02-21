---
layout:     post
title:      "Openshift: Create project, Deploy and Expose an APP."
#date:       201-0-0 11:39:10
author:     Francisco José Rodríguez Rubio
summary:    .
categories: openshift
thumbnail:  openshift
tags:
- openshift
---
### Requirements:
For this example you need openshift origin (okd) deployed locally, with access to the oc command.

---

1) Create project (fran-project)

    oc new-project fran-project

2) select the project

    oc project fran-project

3) Deploy an app in fran-project

    oc new-app kubernetes/guestbook --name=guestbook

4) Create route to expose our app

vim route-guestbook.yaml

~~~
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: guestbook
  name: guestbook
  namespace: fran-project
  selfLink: /apis/route.openshift.io/v1/namespaces/fran-project/routes/guestbook
spec:
  host: guestbook-fran-project.127.0.0.1.nip.io
  port:
    targetPort: 3000-tcp
  to:
    kind: Service
    name: guestbook
    weight: 100
  wildcardPolicy: None
~~~
Create the route:

	oc create -f route-guestbook.yaml

5) Go to [http://guestbook-fran-project.127.0.0.1.nip.io](http://guestbook-fran-project.127.0.0.1.nip.io)

then you will see:

![]({{site.baseurl}}/images/2016-04-01-openshift-deploy-app.jpg)
