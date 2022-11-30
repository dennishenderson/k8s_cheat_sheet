# Controller Manager
Process that monitors state of various cluster componenets in the Kubernetes Cluster.  These controllers rely on the kube-apiserver for functionality but these controllers are the brains of Kubernetes.  All of these controllers are packedged together as part of the "Kube-Controller-Manager"

## Types of Controllers:
* Node-Controller
* Deployment-Controller
* Namespace-Controller
* Replication-Controller
* Service-Account-Controller
* Endpiont-Controller
* Job-Controller
* PV-Protection-Controller
* PV-Binder-Controller
* Stateful-Set
* Replicaset
* CronJob

## Controller Details
<pre>
Node-Controller
Monitors nodes for desired state config leveraging the kube-apiserver.
Node Monitor Period = 5s
Node Monitor Grace Period = 40s
POD Eviction Timeout = 5m

Replication-Controller
Monitors replicatoin controllers across the worker nodes using the kube-apiserver
</pre>

## Viewing Controller-Manager Configuration
Viewing the Controller-Manager configuration varies based on how k8s was setup
<pre>
Command for Docker k8s:
kubectl describe pod kube-controller-manager-docker-desktop -n kube-system

Command for kubeadm:
cat /etc/kubernetes/manifests/kube-controller-manager.yaml

Command for non-kubeadm:
cat /etc/systemd/system/kube-controller-manager.service

Viewing Running Process:
ps -aux | grep kube-controller-manager
</pre>