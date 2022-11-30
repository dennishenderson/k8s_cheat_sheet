# API Server
The Kubernetes API server handles all requests for the Kubernetes cluster.  All kubectl commands issue POST api requests to the kube-apiserver and the api-server is the only k8s cluster service that interacts with the etcd service.

# API Server Local Commands
<pre>
Command to Find your Kubernetes API Server Pod:
kubectl get pods -n kube-system

Command to find Docker Properties run agains the api server pod:
kubectl describe pod kube-apiserver-docker-desktop -n kube-system

Kubeadm API Server Config Location:
cat /etc/kubernetes/manifests/kube-apiserver.yaml

Non-KubeADM API Server Config Location:
cat /etc/systemd/system/kube-apiserver.service

See running process for api server
ps -aux | grep kube-apiserver
</pre>