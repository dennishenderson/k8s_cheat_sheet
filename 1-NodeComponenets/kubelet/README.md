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