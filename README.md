# Kubernetes Cheat Sheet
This repo covers a list of Kubernetes commands to perform various tasks.  It's used as a personal reference guide, however feel free to fork or replicate this if you find it helpful.

## Basic Commands
<pre>
kubectl version
kubectl version -o=yaml
kubectl cluster-info
kubectl run hello-minikube

kubectl get all

kubectl get nodes
</pre>

## Namespaces
<pre>
kubectl get namespaces
kubectl get all -n kube-system
kubectl get pods -n kube-system
</pre>

## Explain Examples
<pre>
kubectl explain pod
kubectl explain service
kubectl explain replicaset
kubectl explain replicationcontroller
kubectl explain deployment
kubectl explain node
</pre>

## YAML Kind | Version Examples
<pre>
POD = v1
Service = v1
ReplicaSet = apps/v1
Deployment = apps/v1
</pre>

## Pod Commands
<pre>
kubectl get pods
kubectl get po
kubectl get pods -o wide
kubectl describe pods
kubectl describe pod nginx

kubectl run nginx --image=nginx

kubectl create -f pod-definition.yaml
kubectl apply -f pod-definition.yaml
kubectl delete -f pod-definition.yaml
kubectl delete pod pod1 pod2 pod3

kubectl edit pod pod1

kubectl exec --stdin --tty nginx -- /bin/bash

kubectl set image pod/nginx nginx=nginx:1.9.1
</pre>

### Search For Pods By Their Labels
<pre>
kubectl get pods --selector app=nginx
kubectl get po -l app=nginx
kubectl get all -l env=prod

Count all the prod web pods:
kubectl get pods -l 'env=prod,tier=web' --no-headers | wc -l
</pre>

### Quickly Create Pod Definition YAML Files
<pre>
kubectl run redis --image=redis --dry-run=client -o yaml > redis-pod.yaml
kubectl get pod nginx -o yaml > nginx-definition.yaml
</pre>

## Replica Sets & Replication Controllers
<pre>
kubectl get replicationcontroller
kubectl get replicaset
kubectl get rs

kubectl describe replicaset replicaset_name
kubectl describe rs replicaset_name
kubectl describe rs

kubectl create -f rc-definition.yaml
kubectl apply -f rc-definition.yaml
kubectl delete -f rc-definition.yaml
kubectl delete replicaset myapp-replicaset
kubectl replace -f replicaset-definition.yaml

kubectl scale --replicas=6 -f replicaset-definition.yaml
kubectl scale --replicas=6 replicaset myapp-replicaset
kubectl scale replicaset myapp-replicaset --replicas=2 

kubectl edit replicaset my-replica-set
</pre>

### Quickly Create Replicaset Definition YAML Files
If the replicaset is alreay running or it was created from a Deployment, you can use the following to generate a yaml file
<pre>
kubectl get replicaset my-replica -o yaml > my-replica-definition.yaml
</pre>

## Deployments
<pre>
kubectl get deployments
kubectl describe deployments
kubectl describe replicaset myapp-replicaset

kubectl create -f deployment.yaml
kubectl apply -f deployment.yaml
kubectl apply --force -f deployment.yaml
kubectl replace -f deployment.yaml
kubectl replace --force -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl delete replicaset myapp-replicaset

kubectl expose deployment nginx --port 80

kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3

kubectl set image deployment/myapp-replicaset nginx=nginx1.9.1
</pre>

### Quickly Create Deployment Definition YAML Files
<pre>
kubectl create deployment httpd-web --image=httpd --replicas=3 --dry-run=client -o=yaml > httpd-dry-run-definition.yaml
</pre>

## Rollouts
Rollouts and rollbacks are done one deployments and can be seen using the below rollout commands.  Additonally there are two types of deployment strategies:
<ul>
    <li>Rolling Update (Default) - This creates a second replicaset and deletes one pod in the old repliaset then recreates a new pod in the new replicaset</li>
    <li>Recreate - destroys all pods in the old replicaset, creates a new replicaset and adds all new pods to it</li>
</ul>
<pre>
kubectl create -f deployment-definition.yaml
kubectl get deployments
kubectl apply -f deployment-definition.yaml
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1

kubectl create -f deployment.yaml --record
kubectl edit deployment myapp-deployment --record

kubectl rollout status deployment/myapp-deployment
kubectl rollout history deployment/myapp-deployment
kubectl rollout undo deployment/myapp-deployment
</pre>

## Services
Three Types of Services: NodePort, ClusterIP, LoadBalancer</br>
If no service type is chosen, ClusterIP is the (default)</br>
When serving multiple pods, a service randomly distributes load and uses session affinity
<pre>
kubectl get services
kubectl get svc
kubectl describe services

kubectl create -f service-definition.yaml
kubectl apply -f service-definition.yaml
kubectl delete -f service-definition.yaml

kubectl delete service myapp-service
</pre>

### Quickly Create Service Definition YAML Files
<pre>
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
kubectl expose pod nginx --port=80 --name nginx-service --dry-run=client -o yaml
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
</pre>

### NodePort
Default NodePort Range: 30000 - 32767
<pre>
</pre>

### ClusterIP
<pre>
</pre>

### LoadBalancer
LoadBalancer service type in a single node lab acts like NodePort, this is really used in most Cloud Providers as a hook to create an external load blancer outside of k8s.
<pre>
</pre>

## Kubernetes System Commands
<pre>
This command is not working on local Docker may work in production
kubectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only

This command gets into the shell of the etcd docker pod with interactive terminal
kubectl exec --stdin --tty etcd-docker-desktop -n kube-system -- /bin/sh
</pre>

## Namespaces
* kube-system - this is the namespace for all the core services of k8s and control plane
* kube-public - this is designed to be used as a space for pods to be shared across all other namespaces
* defaul - default namespace used when working with k8s

<pre>
kubectl get namespaces
kubectl get ns
kubectl get all -n kube-system
kubectl get pods -n kube-system

kubectl get pods --all-namespaces
kbuectl get all --all-namespaces
kubectl get pods -A
kubectl get all -a

kubectl create -f namespace-definition.yaml
kubectl create namespace dev
kubectl delete namespace dev
</pre>

### Changing The Default Namespace
<pre>
kubectl config set-contet $(kubectl config current-context) --namespace=dev
kubectl config set-contet $(kubectl config current-context) --namespace=prod
</pre>

### Calling Another Namespace From A Pod
To call the service "db-service" in a "dev" namespace you could format as follows:
<pre>
db-service.dev.svc.cluster.local

cluster.local - default domain name
svc - service subdomain
dev - namespace
</pre>

## Resource Quotas
Resource quotas can be set on namespaces to limit how many resources can be used
<pre>

</pre>

## Scheduler
If you don't have a scheduler running in a k8s cluster, it's possible to bypass the scheduler and use the nodeName: options key under the spec section of a yaml file.  Optionally you can call the api service directly with a POST see the following link for more details: (https://kubernetes.io/docs/reference/kubernetes-api/cluster-resources/binding-v1/)

## Taints
Taints are applied to nodes to prevent resources from being created on them unless there's a "Toleration" that allows it to be added. By default the master node has a Taint applied to prevent pods from running on them. Types of Taint Effects:
* NoSchedule - New PODs will not be scheduled on the node
* PreferNoSchedule - System will try to prevent pods from being placed on the node but not garonteed
* NoExecute - New PODs will not be scheduled on the node, and any existing pods that do not tolerate the taint will be evicted
<pre>
kubectl taint nodes node1 app=blue:NoSchedule
kubectl taint node node01 key=value:NoExecute
kubectl describe node kubemaster | grep Taint
</pre>

## Tolerations
Tolerations are applied to PODs and what allow any taints on nodes to be tolerated aka ignored when scheduling a POD on a cluster. You can add the example block of optional code to the pod-definition.yaml file under the spec dictionary:
<pre>
spec:
  tolerations:
    - key: "app"
      operator "Equal"
      value: "blue"
      effect: "NoSchedule"
</pre>

## Node Selectors
If you want to limit which pods can be assigned to a list of nodes, you can use labels. This has a limitation that you can only select one label type, this is fixed by using Node Affinity.
<pre>
kubectl label nodes node01 key=value
kubectl label nodes node01 size=large
</pre>

Under the pod-definition you can use the "nodeSelector" optional dictionary under spec:
<pre>
spec:
  nodeSelector:
  size: large
</pre>

## Node Affinity
Allows you to create complex AND OR NOT functionality when choosing which nodes to assign PODs to.  Addtional definition goes under the spec: dictionary.  There are two Affinity Types:
* requiredDuringSchedulingIgnoredDuringExecution
* preferredDuringSchedulingIgnoredDuringExecution
https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/
<pre>
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - matchExpressions:
          - key: size
            operator: In
            values:
            - Large
            - Medium

Optionally:
            operator: NotIn
            values:
            - Small

Optionally:
            operator: Exists
</pre>

## Resource Requirmenets and Limits
CPU, RAM, and Disk can have requirmenets and limits set on PODs.  Below are some things to consider:
* CPU - by default 0.5, represents half a vCPU or Core. can be as small as 0.1 or 100m, or optionally 1m (1/1000th of a vCPU)
* RAM - default max is 512Mi
CPU can not be overprovisioned, Memory can be overprovisioned, but if it's frequent for a pod it will be terminated. Examples Definitions:
<pre>
spec:
  containers:
    resources:
      requests:
        memory: "1Gi"
        cpu: 1
      limits:
        mempory: "2Gi"
        cpu: 2
</pre>

## Daemon Sets
A Daemon Set is like a Replica Set except that it creates 1 pod per node.  This is useful for things like kube-proxy, networking like weave-net and calico, and log monitoring agents so containers can interact with a local pod to the node.
<pre>
kubectl get ds
kubectl get daemonsets
kubectl describe daemonsets monitoring-daemon

kubectl create -f daemon-set-definition.yaml
</pre>

### Quickly Create Daemonset Definition YAML Files
There's no dry-run for a Daemonset but you can ceate a Deployment, then change the kind and delete the replicas spec.
<pre>
kubectl create deployment elasticsearch -n kube-system --image=fluentd-elasticsearch --dry-run=client -o yaml > fluentd.yaml
vim fluentd.yaml
kubectl create -f fluentd.yaml
</pre>

## Static Pods
Kubelet has the ability to run PODs from a manifest path and try to keep them running without the help of the cluster control-plane.  this is typically under /etc/kubernetes/manifests and gets sent when creating the kublet service on the server with the --pod-manifest-path=/... parameter. </br>
Static Pods can be easily identified as they have a postfix of -NodeName, for example etcd-controlplane
<pre>
docker ps

ps -aux | grep kubectl
  look for "--config"

cat /var/lib/kubelet/config.yaml
  staticPodPath: /etc/kubernetes/manifests

ls /etc/kubernetes/manifests/
</pre>