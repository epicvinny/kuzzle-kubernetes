# Kuzzle for Kubernetes
Kubernetes YML files to deploy Kuzzle stack

# Prerequisites

* A VM provider like [VirtualBox](https://www.virtualbox.org/wiki/Downloads) or [KVM](https://www.linux-kvm.org/page/Main_Page#)
* Minikube (prefer version [__v0.25.2__](https://github.com/kubernetes/minikube/releases/tag/v0.25.2))
* Kubectl (prefer version [__v1.9.4__](https://storage.googleapis.com/kubernetes-release/release/v1.9.4/bin/linux/amd64/kubectl))

# Deployment

```bash
$ minikube start --kubernetes-version v1.9.4
Starting local Kubernetes v1.9.4 cluster...
Starting VM...
Downloading Minikube ISO
 142.22 MB / 142.22 MB [============================================] 100.00% 0s
Getting VM IP address...
Moving files into cluster...
Downloading localkube binary
 163.02 MB / 163.02 MB [============================================] 100.00% 0s    
 65 B / 65 B [======================================================] 100.00%
Setting up certs...
Connecting to cluster...                                                                                                                
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
Loading cached images from config file.
# Wait ~ 1min to let all addons get ready (especially 'kube-dns') ;

# Here running order is important ;
# Wait until each container is started
# before proceeding to the next step ;
$ kubectl create -f elasticsearch.yml
service "elasticsearch" created
deployment "es-node" created

$ kubectl create -f redis.yml
service "redis" created
deployment "redis-node" created

$ kubectl create -f kuzzle.yml
service "kuzzle" created
deployment "kuzzle-node" created

$ kubectl get pods,deployments,services,endpoints
NAME                             READY     STATUS    RESTARTS   AGE
po/es-node-67b6c9c974-rrqp8      1/1       Running   0          2m
po/kuzzle-node-889974d64-w8v7j   1/1       Running   0          36s
po/redis-node-55c6b78bf5-pnrdx   1/1       Running   0          1m

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/es-node       1         1         1            1           2m
deploy/kuzzle-node   1         1         1            1           31s
deploy/redis-node    1         1         1            1           1m

NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
svc/elasticsearch   ClusterIP   10.105.213.154   <none>        9200/TCP    2m
svc/kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP     5m
svc/kuzzle          ClusterIP   10.99.43.230     <none>        7512/TCP   33s
svc/redis           ClusterIP   10.99.61.2       <none>        6379/TCP    1m

NAME               ENDPOINTS         AGE
ep/elasticsearch   172.17.0.4:9200    1m
ep/kubernetes      10.0.2.15:8443     5m
ep/kuzzle          172.17.0.6:7512   15s
ep/redis           172.17.0.5:6379   45s

# When all containers are up and ready you can test your Kuzzle server
$ curl $(minikube ip):7512/_now | jq                                                  

{
  "requestId": "fadcca66-84c5-4f65-9324-8d30c674cbd9",
  "status": 200,
  "error": null,
  "controller": "server",
  "action": "now",
  "collection": null,
  "index": null,
  "volatile": null,
  "result": {
    "now": 1524484214575
  }
}

```

# TODO

- [x] Make services talk to each other by their names (kube-dns)
- [ ] Make this stack working with an ES cluster (Scalability/LoadBalancing)
- [ ] Same with Redis cluster

# [LICENSE](https://github.com/kuzzleio/kuzzle-kubernetes/blob/master/LICENSE)
