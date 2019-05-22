+++
menutitle = "ClusterIP"
date = 2018-12-29T17:15:52Z
weight = 3
chapter = false
pre = "<b>- </b>"
+++

# Service with type `clusterIP`
It exposes the service on a cluster-internal IP.

When we expose a pod using `kubectl expose` command , we are creating a service object in API.

Choosing this value makes the service only reachable from within the cluster. **This is the default ServiceType**.

We can see the `Service` spec using `--dry-run` & `--output=yaml`

# Lets create a deployment 

```yaml
piVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
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
      - name: nginx-ctr
        image: nginx:1.15.4
        ports:
        - containerPort: 80
```
# Deploy it 

```script
$ kubectl apply -f deploy.yaml 
```

# Create a service for the deployment 

```yaml 
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  labels:
    app: nginx
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
```
# Deploy the service 

```script
$ kubectl apply -f clusterip-svc.yaml
```

# Check if services are running fine

```script
$ kubectl get svc 
```

# Verify the DNS service via nslookup

- Run another nginx deployment and use dnslookup by service name 

```script 
$ kubectl run demo --it --rm --image=nginx:latest /bin/sh
```

- Inside the pod run below 

```script 
$ apt-get update; apt-get install dnsutils; nslookup nginx-svc
```
If everything works then you should see a response. 

# Delete deployment and service 

```script
$ kubectl delete svc nginx-svc
$ kubectl delete deploy nginx-deployment
```

