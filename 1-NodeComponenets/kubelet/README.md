# Kubelet
This is the process that manages the actions on the k8s nodes.  It's responsible for registering the node in the cluster.  Creating PODs using the container runtime. Monitors the Node and PODs.

## Kubelet Install
kubelet does not automatically get installed when running kubeadm. Must always be manually be installed on each k8s worker node.

## Viewing Kubelet Configuration
Viewing the Scheduler configuration varies based on how k8s was setup
<pre>
Viewing Running Process:
ps -aux | grep kubelet
</pre>

## Monitoring
Kubelet contains a sub componenet known as cAdvisor which sends metrics to the Metrics Server for in-memory logs of CPU, memory, logs data etc.  https://github.com/kubernetes-sigs/metrics-server

<pre>
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

kubectl top node
kubectl top pod
</pre>

## Logging
<pre>
kubectl logs -f pod-name
kbuectl logs pod-name
</pre>