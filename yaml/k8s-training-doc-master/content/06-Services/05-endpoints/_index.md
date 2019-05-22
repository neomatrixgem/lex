+++
menutitle = "Endpoints"
date = 2018-12-29T17:15:52Z
weight = 5
chapter = false
pre = "<b>- </b>"
+++

# Pods behind a service.
![NodePod](nodeport.png?classes=shadow)
Lets `describe` the service to see how the mapping of Pods works in a service object.

(Yes , we are slowly moving from general wordings to pure kubernetes terms)
```yaml
$ kubectl describe service coffee
Name:                     coffee
Namespace:                default
Labels:                   run=coffee
Annotations:              <none>
Selector:                 run=coffee
Type:                     NodePort
IP:                       192.168.10.86
Port:                     <unset>  80/TCP
TargetPort:               9090/TCP
NodePort:                 <unset>  30391/TCP
Endpoints:                10.10.1.13:9090
Session Affinity:         None
External Traffic Policy:  Cluster
```

Here the label `run=coffee` is the one which creates the mapping from service to Pod.

Any pod with label `run=coffee` will be mapped under this service.

Those mappings are called `Endpoints`.

Lets see the `endpoints` of `service` `coffee`
```shell
$ kubectl get endpoints  coffee
NAME     ENDPOINTS         AGE
coffee   10.10.1.13:9090   3h48m
```

As of now only one pod endpoint is mapped under this service.

lets create one more Pod with same label and see how it affects endpoints.

```
$ kubectl run coffee01 --image=ansilh/demo-coffee --restart=Never --labels=run=coffee
```

Now we have one more Pod
```
$ kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
coffee     1/1     Running   0          15h
coffee01   1/1     Running   0          6s
```

Lets check the endpoint

```
$ kubectl get endpoints  coffee
NAME     ENDPOINTS                         AGE
coffee   10.10.1.13:9090,10.10.1.19:9090   3h51m
```

Now we have two Pod endpoints mapped to this service.
So the requests comes to coffee service will be served from these pods in a round robin fashion.
