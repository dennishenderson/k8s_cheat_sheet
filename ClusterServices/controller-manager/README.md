# Controller Manager
Process that monitors state of various cluster componenets in the Kubernetes Cluster

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
</pre>
Node-Controller
Monitors nodes for desired state config leveraging the kube-apiserver.
Node Monitor Period = 5s
Node Monitor Grace Period = 40s
POD Eviction Timeout = 5m

Replication-Controller
Monitors replicatoin controllers across the worker nodes using the kube-apiserver
</pre>
