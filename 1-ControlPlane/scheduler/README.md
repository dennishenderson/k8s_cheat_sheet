# Kube-Scheduler
This process is responsible for choosing which node can be selected for a given pod assignment.

## Viewing Scheduler Configuration
Viewing the Scheduler configuration varies based on how k8s was setup
<pre>
Command for Docker k8s:
kubectl describe pod kube-scheduler-docker-desktop -n kube-system

Command for kubeadm:
cat /etc/kubernetes/manifests/kube-scheduler.yaml

Command for non-kubeadm:
cat /etc/systemd/system/kube-scheduler.service

Viewing Running Process:
ps -aux | grep kube-scheduler
</pre>