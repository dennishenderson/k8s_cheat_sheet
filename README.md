# Kubernetes Cheat Sheet
## Basic commands:
<pre>
kubectl version
kubectl version -o=yaml
kubectl cluster-info
kubectl run hello-minikube

kubectl get nodes
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

## YAML Kind | Version Examples
<pre>
POD = v1
Service = v1
ReplicaSet = apps/v1
Deployment = apps/v1
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

## Quickly Create YAML Deploy Files
<pre>
kubectl run redis --image=redis --dry-run=client -o yaml > redis-deployment.yaml
</pre>

## Replication Controllers and Replica Sets
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