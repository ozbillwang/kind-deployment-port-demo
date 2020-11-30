# Access service running in KIND (Kubernetes in Docker) 

A show case on how to export your local port, so you can access the application and service running in KIND (kubernetes in docker)

### Steps

* create kind cluster

```
$ git clone https://github.com/ozbillwang/kind-deployment-port-demo.git
$ cd kind-deployment-port-demo

$ brew install kind
$ kind create cluster --config kind-config.yaml
```
Make sure you can see the nodes

```
$ kubectl get nodes

NAME                 STATUS   ROLES    AGE   VERSION
kind-control-plane   Ready    master   19m   v1.18.2
kind-worker          Ready    <none>   19m   v1.18.2
```
Make sure the port has been redirected from host to kind worker's container
```
$ docker ps

CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                       NAMES
06fc7e02d4b6        kindest/node:v1.18.2   "/usr/local/bin/entr…"   20 minutes ago      Up 20 minutes       0.0.0.0:80->32000/tcp       kind-worker
2a06cdabe9c7        kindest/node:v1.18.2   "/usr/local/bin/entr…"   20 minutes ago      Up 20 minutes       127.0.0.1:63527->6443/tcp   kind-control-plane
```
From above output, host port 32000 has been redirected from kind worker's port 80

### deploy nginx service

```
$ kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml

# check if nginx pods are running
$ kubectl get pods

NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-6b474476c4-4r2gw   1/1     Running   0          23m
nginx-deployment-6b474476c4-8z2ds   1/1     Running   0          23m
nginx-deployment-6b474476c4-h9b5m   1/1     Running   0          23m
```

### Using a Service to Expose Your App

```
$ kubectl apply -f service.yaml

$ kk get service
NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP   10.96.0.1      <none>        443/TCP        24m
nginx-deployment   NodePort    10.103.49.97   <none>        80:32000/TCP   16m
```
### Access nginx service

Access http://localhost

### Reference

https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
