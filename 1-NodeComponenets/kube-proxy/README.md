# Kube Proxy
Process used to watch for services that get created or deleted on the cluster.  This then creates IPRouteTables local to each node that maps the virtual IP of the service the the actual IP of the node containing the running pod(s)

## Viewing Proxy Configuration
Viewing the Proxy configuration varies based on how k8s was setup
<pre>
Command to find kube-proxy:
kubectl get pods -n kube-system
kubectl get daemonset -n kube-system

Command for Docker k8s:
kubectl describe pod kube-proxy-cqc5l -n kube-system

Command for kubeadm:
cat /etc/kubernetes/manifests/kube-scheduler.yaml

Command for non-kubeadm:
cat /etc/systemd/system/kube-scheduler.service

Viewing Running Process:
ps -aux | grep kube-scheduler
</pre>