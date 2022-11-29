# Kubernetes Cheat Sheet
## Kubernetes commands:
<pre>
kubectl run hello-minikube
kubectl cluster-info
kubectl get nodes

kubectrl run nginx --image=nginx

kubectl get pods
kubectl get pods -o wide
kubectl describe pods
kubectl describe pod nginx

kubectl exec --stdin --tty nginx -- /bin/bash

kubectl create -f pod-definition.yaml
kubectl apply -f pod-definition.yaml

kubectl delete pod pod1 pod2 pod3
</pre>

## YAML Kind | Version Examples
<pre>
POD = v1
Service = v1
ReplicaSet = apps/v1
Deployment = apps/v1
</pre>

## Quickly Create YAML Deploy Files
<pre>
kubectl run redis --image=redis --dry-run=client -o yaml > redis-deployment.yaml
</pre>

## Replication Controllers and Replica Sets
<pre>
kubectl explain replicaset
kubectl get rs

kubectl get replicationcontroller
kubectl get replicaset

kubectl create -f rc-definition.yaml
kubectl apply -f rc-definition.yaml
kubectl delete replicaset myapp-replicaset

kubectl describe replicaset replicaset_name
</pre>

### Scale the Replica Set
<pre>
kubectl replace -f replicaset-definition.yaml
kubectl scale --replicas=6 -f replicaset-definition.yaml
kubectl scale --replicas=6 replicaset myapp-replicaset

kubectl edit replicaset my-replica-set
kubectl scale replicaset myapp-replicaset --replicase=2 
</pre>