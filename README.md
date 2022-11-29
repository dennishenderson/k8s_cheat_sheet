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
kubectl get pods -o wide
kubectl describe pods
kubectl describe pod nginx

kubectrl run nginx --image=nginx

kubectl create -f pod-definition.yaml
kubectl apply -f pod-definition.yaml
kubectl delete -f pod-definition.yaml
kubectl delete pod pod1 pod2 pod3

kubectl edit pod pod1

kubectl exec --stdin --tty nginx -- /bin/bash
</pre>

### Quickly Create Pod Definition YAML Files
<pre>
kubectl run redis --image=redis --dry-run=client -o yaml > redis-pod.yaml
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

## Deployments
<pre>
kubectl get deployments
kubectl describe deployments
kubectl describe replicaset myapp-replicaset

kubectl create -f deployment.yaml
kubectl apply -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl delete replicaset myapp-replicaset

kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3
</pre>

### Quickly Create Deployment Definition YAML Files
<pre>
kubectl create deployment httpd-web --image=httpd --replicas=3 --dry-run=client -o=yaml > httpd-dry-run-definition.yaml
</pre>