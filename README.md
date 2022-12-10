# Kubernetes Cheat Sheet
This repo covers a list of Kubernetes commands to perform various tasks.  It's used as a personal reference guide, however feel free to fork or replicate this if you find it helpful.  You can also view the official cheat sheet here:
https://kubernetes.io/docs/reference/kubectl/cheatsheet/

## Basic Commands
<pre>
kubectl version
kubectl version -o=yaml
kubectl cluster-info
kubectl run hello-minikube

kubectl get all

kubectl get nodes
</pre>

## Namespaces
<pre>
kubectl get namespaces
kubectl get all -n kube-system
kubectl get pods -n kube-system
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
kubectl get po
kubectl get pods -o wide
kubectl describe pods
kubectl describe pod nginx

kubectl run nginx --image=nginx

kubectl create -f pod-definition.yaml
kubectl apply -f pod-definition.yaml
kubectl delete -f pod-definition.yaml
kubectl delete pod pod1 pod2 pod3

kubectl edit pod pod1

kubectl exec --stdin --tty nginx -- /bin/bash

kubectl set image pod/nginx nginx=nginx:1.9.1
</pre>

### Search For Pods By Their Labels
<pre>
kubectl get pods --selector app=nginx
kubectl get po -l app=nginx
kubectl get all -l env=prod

Count all the prod web pods:
kubectl get pods -l 'env=prod,tier=web' --no-headers | wc -l
</pre>

### Quickly Create Pod Definition YAML Files
<pre>
kubectl run redis --image=redis --dry-run=client -o yaml > redis-pod.yaml
kubectl get pod nginx -o yaml > nginx-definition.yaml
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

### Quickly Create Replicaset Definition YAML Files
If the replicaset is alreay running or it was created from a Deployment, you can use the following to generate a yaml file
<pre>
kubectl get replicaset my-replica -o yaml > my-replica-definition.yaml
</pre>

## Deployments
<pre>
kubectl get deployments
kubectl describe deployments
kubectl describe replicaset myapp-replicaset

kubectl create -f deployment.yaml
kubectl apply -f deployment.yaml
kubectl apply --force -f deployment.yaml
kubectl replace -f deployment.yaml
kubectl replace --force -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl delete replicaset myapp-replicaset

kubectl expose deployment nginx --port 80

kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3

kubectl set image deployment/myapp-replicaset nginx=nginx1.9.1
</pre>

### Quickly Create Deployment Definition YAML Files
<pre>
kubectl create deployment httpd-web --image=httpd --replicas=3 --dry-run=client -o=yaml > httpd-dry-run-definition.yaml
</pre>

## Rollouts
Rollouts and rollbacks are done one deployments and can be seen using the below rollout commands.  Additonally there are two types of deployment strategies:
<ul>
    <li>Rolling Update (Default) - This creates a second replicaset and deletes one pod in the old repliaset then recreates a new pod in the new replicaset</li>
    <li>Recreate - destroys all pods in the old replicaset, creates a new replicaset and adds all new pods to it</li>
</ul>
<pre>
kubectl create -f deployment-definition.yaml
kubectl get deployments
kubectl apply -f deployment-definition.yaml
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1

kubectl create -f deployment.yaml --record
kubectl edit deployment myapp-deployment --record

kubectl rollout status deployment/myapp-deployment
kubectl rollout history deployment/myapp-deployment
kubectl rollout undo deployment/myapp-deployment
</pre>

## Services
Three Types of Services: NodePort, ClusterIP, LoadBalancer</br>
If no service type is chosen, ClusterIP is the (default)</br>
When serving multiple pods, a service randomly distributes load and uses session affinity
<pre>
kubectl get services
kubectl get svc
kubectl describe services

kubectl create -f service-definition.yaml
kubectl apply -f service-definition.yaml
kubectl delete -f service-definition.yaml

kubectl delete service myapp-service
</pre>

### Quickly Create Service Definition YAML Files
<pre>
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
kubectl expose pod nginx --port=80 --name nginx-service --dry-run=client -o yaml
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
</pre>

### NodePort
Default NodePort Range: 30000 - 32767
<pre>
</pre>

### ClusterIP
<pre>
</pre>

### LoadBalancer
LoadBalancer service type in a single node lab acts like NodePort, this is really used in most Cloud Providers as a hook to create an external load blancer outside of k8s.
<pre>
</pre>

## Kubernetes System Commands
<pre>
This command is not working on local Docker may work in production
kubectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only

This command gets into the shell of the etcd docker pod with interactive terminal
kubectl exec --stdin --tty etcd-docker-desktop -n kube-system -- /bin/sh
</pre>

## Namespaces
* kube-system - this is the namespace for all the core services of k8s and control plane
* kube-public - this is designed to be used as a space for pods to be shared across all other namespaces
* defaul - default namespace used when working with k8s

<pre>
kubectl get namespaces
kubectl get ns
kubectl get all -n kube-system
kubectl get pods -n kube-system

kubectl get pods --all-namespaces
kbuectl get all --all-namespaces
kubectl get pods -A
kubectl get all -a

kubectl create -f namespace-definition.yaml
kubectl create namespace dev
kubectl delete namespace dev
</pre>

### Changing The Default Namespace
<pre>
kubectl config set-contet $(kubectl config current-context) --namespace=dev
kubectl config set-contet $(kubectl config current-context) --namespace=prod
</pre>

### Calling Another Namespace From A Pod
To call the service "db-service" in a "dev" namespace you could format as follows:
<pre>
db-service.dev.svc.cluster.local

cluster.local - default domain name
svc - service subdomain
dev - namespace
</pre>

## Resource Quotas
Resource quotas can be set on namespaces to limit how many resources can be used
<pre>

</pre>

## Scheduler
If you don't have a scheduler running in a k8s cluster, it's possible to bypass the scheduler and use the nodeName: options key under the spec section of a yaml file.  Optionally you can call the api service directly with a POST see the following link for more details: (https://kubernetes.io/docs/reference/kubernetes-api/cluster-resources/binding-v1/)

## Taints
Taints are applied to nodes to prevent resources from being created on them unless there's a "Toleration" that allows it to be added. By default the master node has a Taint applied to prevent pods from running on them. Types of Taint Effects:
* NoSchedule - New PODs will not be scheduled on the node
* PreferNoSchedule - System will try to prevent pods from being placed on the node but not garonteed
* NoExecute - New PODs will not be scheduled on the node, and any existing pods that do not tolerate the taint will be evicted
<pre>
kubectl taint nodes node1 app=blue:NoSchedule
kubectl taint node node01 key=value:NoExecute
kubectl describe node kubemaster | grep Taint
</pre>

## Tolerations
Tolerations are applied to PODs and what allow any taints on nodes to be tolerated aka ignored when scheduling a POD on a cluster. You can add the example block of optional code to the pod-definition.yaml file under the spec dictionary:
<pre>
spec:
  tolerations:
    - key: "app"
      operator "Equal"
      value: "blue"
      effect: "NoSchedule"
</pre>

## Node Selectors
If you want to limit which pods can be assigned to a list of nodes, you can use labels. This has a limitation that you can only select one label type, this is fixed by using Node Affinity.
<pre>
kubectl label nodes node01 key=value
kubectl label nodes node01 size=large
</pre>

Under the pod-definition you can use the "nodeSelector" optional dictionary under spec:
<pre>
spec:
  nodeSelector:
  size: large
</pre>

## Node Affinity
Allows you to create complex AND OR NOT functionality when choosing which nodes to assign PODs to.  Addtional definition goes under the spec: dictionary.  There are two Affinity Types:
* requiredDuringSchedulingIgnoredDuringExecution
* preferredDuringSchedulingIgnoredDuringExecution
https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/
<pre>
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - matchExpressions:
          - key: size
            operator: In
            values:
            - Large
            - Medium

Optionally:
            operator: NotIn
            values:
            - Small

Optionally:
            operator: Exists
</pre>

## Resource Requirmenets and Limits
CPU, RAM, and Disk can have requirmenets and limits set on PODs.  Below are some things to consider:
* CPU - by default 0.5, represents half a vCPU or Core. can be as small as 0.1 or 100m, or optionally 1m (1/1000th of a vCPU)
* RAM - default max is 512Mi
CPU can not be overprovisioned, Memory can be overprovisioned, but if it's frequent for a pod it will be terminated. Examples Definitions:
<pre>
spec:
  containers:
    resources:
      requests:
        memory: "1Gi"
        cpu: 1
      limits:
        mempory: "2Gi"
        cpu: 2
</pre>

## Daemon Sets
A Daemon Set is like a Replica Set except that it creates 1 pod per node.  This is useful for things like kube-proxy, networking like weave-net and calico, and log monitoring agents so containers can interact with a local pod to the node.
<pre>
kubectl get ds
kubectl get daemonsets
kubectl describe daemonsets monitoring-daemon

kubectl create -f daemon-set-definition.yaml
</pre>

### Quickly Create Daemonset Definition YAML Files
There's no dry-run for a Daemonset but you can ceate a Deployment, then change the kind and delete the replicas spec.
<pre>
kubectl create deployment elasticsearch -n kube-system --image=fluentd-elasticsearch --dry-run=client -o yaml > fluentd.yaml
vim fluentd.yaml
kubectl create -f fluentd.yaml
</pre>

## Static Pods
Kubelet has the ability to run PODs from a manifest path and try to keep them running without the help of the cluster control-plane.  this is typically under /etc/kubernetes/manifests and gets sent when creating the kublet service on the server with the --pod-manifest-path=/... parameter. </br>
Static Pods can be easily identified as they have a postfix of -NodeName, for example etcd-controlplane
<pre>
docker ps

ps -aux | grep kubectl
  look for "--config"

cat /var/lib/kubelet/config.yaml
  staticPodPath: /etc/kubernetes/manifests

ls /etc/kubernetes/manifests/
</pre>

## Logs & Monitoring
logging and monitoring in k8s is pretty basic, you need to setup something like promethius or datadog for advanced logging and log file stores.  Logs are stored in memory in the cluster by default, and monitoring is basic.
<pre>
Continuous log with -f flag:
kubectl logs -f pod-name

2 containers in a single pod:
kubectl logs pod-name container-name

kubectl log pod/pod-name
kubectl log service-name
</pre>

Monitoring can be installed using a metrics-server if not already inistalled as a deployment.
<pre>
kubectl top node
kubectl top pod
</pre>

## Commands & Arguments
Commands and Arguments can be passed to kubernetes containers just like docker.  They can also override the docker "ENTRYPOINT" and "CMD" commands and arguments.
* ENTRYPOINT - this is a docer key that represents the command to be run in a container.  The replacement for this in kubernetes is "command" under the containers dictionary
* CMD - this docker key is replaced by "args" under containers to pass arguments
<pre>
spec:
  containers:
    - name: nginx
      image: nginx
      command: "sleep"
      args: "5000"
</pre>

## Environment Variables
Environment varaibles can be passed as key:value pairs in an array under the spec.containers.env
<pre>
spec:
  containers:
    - name: nginx
      image: nginx
      env:
        - name: env_var
          value: some_value
</pre>

You can pull single environment variables as well from configmaps and secrets
<pre>
spec:
  containers:
    - name: nginx
      image: nginx
      env:
        name: APP_COLOR
        valueFrom:
          configMapKeyRef:
            name: app-config
            key: APP_COLOR

spec:
  containers:
    - name: nginx
      image: nginx
      env:
        name: DB_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-config
            key: DB_PASSWORD
</pre>

## ConfigMaps
ConfigMaps are used as non-sensitive key:value pairs to be used by definition files and resources in your environment.
https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/
<pre>
kubectl get cm
kubectl get configmaps
kubectl describe cm
kubectl describe configmaps

Imperative Commands:
kubectl create configmap config-name --from-literal=KEY=value
kubectl create configmap nginx-db --from-literal=DB=redis

kubectl create configmap app-config \
  app-config --from-literal=APP_COLOR=blue \
             --from-literal=APP_MOD=prod

kubectl create configmap config-name --from-file=./file-path
kubectl create configmap app-config --from-file=app_config.properties

Declarative Commands:
kubectl create -f config-map-definition.yaml
</pre>

### Using ConfigMaps in pod-definition files:
You can leverage the spec.containers.envFrom list to call config maps.
<pre>
spec:
  containers:
    - name: nginx
      image: nginx
      envFrom:
        configMapRef:
            name: nginx-config
</pre>

## Secrets
Secrets are like ConfigMaps except that they are encrypted and used for sensitive data such as usernames and passwords.  There are 3 types of secrets:
* docker-registry - Create a secret for use with a Docker registry
* generic - Create a secret from a local file, directory, or literal value
* tls - Create a TLS secret
<pre>
kubectl get secrets
kubectl describe secrets

Imperative Commands:
kubectl create secret generic secret-name --from-literal=KEY=value
kubectl create secret generic secret-mongo-db --from-literal=DB_PASSWORD=abc123

kubectl create secret generic \
  secret-mongo-db --from-literal=DB_USER=user \
                  --from-literal=DB_PASSWORD=password

kubectl create secret generic secret-name --from-file=./file-path
kubectl create secret generic secret-name --from-file=app_config.properties

Declarative Commands:
kubectl create -f secret-definition.yaml
</pre>

### Base64 Encoding & Decoding
To encode your string literals you need to do the following from a termal
<pre>
echo -n 'password' | base64
cGFzc3dvcmQ=

echo -n 'cGFzc3dvcmQ=' | base64 --decode
password
</pre>

### Using Secrets in pod-definition files:
You can leverage the spec.containers.envFrom list to call secrets.
<pre>
spec:
  containers:
    - name: nginx
      image: nginx
      envFrom:
        secretRef:
            name: app-confg

env:
  - name: DB_Password
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: DB_Password

volumes:
  - name: app-secret-volume
    secret:
      secretName: app-secret
</pre>


## Init Containers
Used for a container that needs to run to completion during the initial run of a container.  Perhaps building a file for a web server before it starts, or looking for a DB...  All initContainers are run SEQUENTIALLY before the regular containers run.
<pre>
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
</pre>

## Cluster Maintenance
<pre>
kube-controller-manager --pod-eviction-timeout=5m0s ...

# Puts a node in maintenance mode and stops and creates processes in other nodes
kubectl drain node01

# marks the node schedulable again
kubectl uncordon node01

# marks a node non-scheduable
kubectl cordon node01
</pre>

## Kubeadm Upgrade
You can only upgrade 1 minor release at a time and must do them in order.
* Release Format - v1.13.2 (major_release.minor_release.patch)
https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
<pre>
Check the status of your nodes versions:
kubeadm upgrade plan

1. Download and update kubeadm first
apt-get upgrade -y kubeadm=1.12.0-00

2. apply the upgrade to your cluster
kubeadm upgrade apply v1.12.0

3. Upgrade kubelet on the master node
# Shows the version of the kubelets on the servers:
kubeadm get nodes
# download and install kubelet
apt-get upgrade -y kubelet=1.12.0-00
systemctl restart kubelet

4. Rolling Updates On Each Worker Node
# Maintenance Mode for a node, and re-creates workload across other nodes (if possible)
kubectl drain node01
# Installs update on each worker node
apt-get upgrade -y kubeadm=1.12.0-00
apt-get upgrade -y kubelet=1.12.0-00
kubeadm upgrade node config --kublet-version v1.12.0
systemctl restart kubelet
# Mark node as schedulable
kubectl uncrodon node01
# Reapeat this step for remaining nodes
</pre>

## Backup & Restore
* kubernetes docs - https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster
* Restore from etcd backup - https://github.com/etcd-io/website/blob/main/content/en/docs/v3.5/op-guide/recovery.md
<pre>
kubectl get all -A -o yaml > all-deploy-services.yaml

# ETCD Service has a directory for all configuration
--data-dir=/var/lib/etcd

# Snapshot etcd
ETCDCTL_API=3 etcdctl \
 snapshot save snapshot.db

# Check snapshot status
ETCDCTL_API=3 etcdctl \
  snapshot status snapshot.db

# Restore Server, stop kube-apiserver, restore etcd snapshot
service kube-apiserver stop

ETCDCTL_API=3 etcdctl \
  snapshot restore snapshot.db \
  --data-dir /var/lib/etcd-from-backup

systemctl daemon-reload
service etcd restart
service kube-apiserver start

# NOTE: when running the etcd commands you also need to include the following
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.crt \
  --cert=/etc/etcd/etcd-server.cert \
  --key=/etc/etcd/etcd-server.key

# Identify ETCD Settings
kubectl describe pod etcd-controlplane -n kube-system

# Example Backup
ETCDCTL_API=3 etcdctl snapshot save \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.cert \
  --key=/etc/kubernetes/pki/etcd/server.key \
  /tmp/snapshot.db

# Example Restore
ETCDCTL_API=3 etcdctl snapshot restore \
  --data-dir /var/lib/etcd-from-backup \
  /tmp/snapshot.db

# Update static etcd.yaml file to point to the new restore volume
vim /etc/kubernetes/manifests/etcd.yaml
# modify hostPath directory
- hostPath:
    path: /var/lib/etcd-from-backup
    type: DirectoryOrCreate
  name: etcd-data
</pre>

## Multiple Clusters
When working with clusters you need to change CONTEXTS you can use the following commands to look around:
<pre>
kubectl config get-clusters
kubectl config get-contexts
kubectl config view

# Change Clusters
kubectl config use-context {Cluster_Name}

# Find a list of ETCD members:
ETCDCTL_API=3 etcdctl \
 --endpoints=https://127.0.0.1:2379 \
 --cacert=/etc/etcd/pki/ca.pem \
 --cert=/etc/etcd/pki/etcd.pem \
 --key=/etc/etcd/pki/etcd-key.pem \
  member list
</pre>

## Secure Copy Files
If you need to move files between nodes use scp
<pre>
scp cluster1-controlplane:/opt/cluster1.db /opt
</pre>

## External etcd server restore
If you have etcd installed on an external server and not as a pod in staced mode you need to run a few differnet commands
<pre>
# copy file to etcd server
scp /opt/cluster2.db etcd-server:/root

# Restore DB
etcd-server ~ ➜  ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/etcd/pki/ca.pem --cert=/etc/etcd/pki/etcd.pem --key=/etc/etcd/pki/etcd-key.pem snapshot restore /root/cluster2.db --data-dir /var/lib/etcd-data-new

#update data dir
vi /etc/systemd/system/etcd.service

# change ownership to let etcd service have run access 
chown -R etcd:etcd /var/lib/etcd-data-new
ls -ld /var/lib/etcd-data-new/

# restart service
systemctl daemon-reload 
systemctl restart etcd
</pre>

## Kubernetes Authentication Types
There are multiple ways you can secure kubernetes.  All authentication (user access) has to be done external to kubernetes

### Basic File Authentication
The kube-api-server can read a csv file using the below configuration and format
<pre>
# Config File on API Server Setup
--basic-auth-file=user-details.csv

# Sample User Data in CSV
password1,user1,u0001,group1
password2,user2,u0002,group2
password3,user3,u0003,group3

# Call the API server using a curl command
curl -v -k https://master-node-ip:6443/api/v1/pods -u "user1:password123"
</pre>

### Token File Authentication
<pre>
# Config File on API Server Setup
--token-auth-file=user-details.csv

# Sample User Data in CSV
...Some_Hex_Code_For_User_Token...,user1,u0001,group1
...Some_Hex_Code_For_User_Token...,user2,u0002,group2
...Some_Hex_Code_For_User_Token...,user3,u0003,group3

# Call the API server using a curl command
curl -v -k https://master-node-ip:6443/api/v1/pods --header "Authroization: Bearer ...Some_Hex_Code_For_User_Token..."
</pre>

### SSH Based Authentication
You can leverage public / private key pairs to secure Kubernetes
<pre>
# Create an SSH key pair (id_rsa, id_rsa.pub)
ssh-keygen

# default public key path on servers
/.ssh/authorized_keys

# Open SSL Keygen
openssl genrsa -out my-bank.key 1024
  Output: my-bank.key

openssl rsa -in my-bank.key -pubout > mybank.pem
  Output: my-bank.key mybank.pem

# Create a certificate signed request using open ssl
openssl req -new -key my-bank.key -out my-bank.csr -subj "/C=US/ST=CA/O=MyOrg,Inc./CN=my-bank.com"
  Output: my-bank.key my-bank.csr
</pre>

### Generating a CA Certificate SSL Key using openssl
<pre>
# Generate Key
openssl genrsa -out ca.key 2048
  Output: ca.key

# Certificate Signing Request
openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
  Output: ca.csr

# Sign Certificates
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
  Output: ca.crt
</pre>

### Generating Admin User SSL Key using openssl
<pre>
# Generate Key
openssl genrsa -out admin.key 2048
  Output: admin.key

# Certificate Signing Request
openssl req -new -key admin.key -subj "/CN=kube-admin" -out admin.csr
  Output: admin.csr

# Sign Certificate Using the ca.crt and ca.key
openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -out admin.crt
  Output: ca.crt
</pre>

### Generating SSL key for the Kube API Server
Becasue the Kube API Server us called by so many different names you need to use SAN certs (Subject Alternative Name) with multple endpoints.  Possible combinations to call the API server:
* kubernetes
* Kubernetes.default
* kubernetes.defualt.svc
* kubernetes.default.svc.cluster.local
* internal ip (10.96.0.1)
* external ip (172.17.0.87)
<pre>
# Generating a CSR for API Server
openssl req -new -key apiserver.key -subj "/CN=kube-apiserver" -out apiserver.csr -config openssl.cnf
  Output: apiserver.csr

# Create an openssl.cnf file with the following:
[req]
req_extensions = v3_req
distinguised_name = req_distinguished_name
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation,
subjectAltName = @alt_names
[alt_names]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.cluster.local
IP.1 = 10.96.0.1
IP.2 = 172.17.0.87

# Sign the csr with the CA cert
openssl x509 -req -in apiserver.csr -CA ca.crt -CAkey ca.key -out apiserver.crt
</pre>

### Decoding a certificate to check for issues
<pre>
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
</pre>

### Checking for logs with certificate issues
https://github.com/mmumshad/kubernetes-the-hard-way/tree/master/tools
<pre>
# If installed "The Hard Way"
journalctl -u etcd.service -l

# If installed with kubeadm check the pods
kubectl logs etcd-master

# If the api server is down, you may need to use Docker
docker ps -a
docker logs ...container_id...
</pre>

## Using the Certificate API in Kubectl
You can request certificates using the certificate signing request function within kubectl.  This allows you to create certificates for users in a controlled manner.
https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
<pre>
# generate rsa key
openssl genrsa -out jane.key 2048

# sample output of jane.key
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA1BvGW7EPkOrsqlZzDa+pzsvPaLskN7LuCaoTQhniGInZOifF
...
IYiXWasR6yIcbC3oRhW4kU1WVCzBSvSTY2y6ocuUab+9PC8CpeXx7g==
-----END RSA PRIVATE KEY-----


# convert key to csr
openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr

# sample output of jane.csr
-----BEGIN CERTIFICATE REQUEST-----
MIICVDCCATwCAQAwDzENMAsGA1UEAwwEamFuZTCCASIwDQYJKoZIhvcNAQEBBQAD
...
EazTcnadsmTkm1s/viQBHdIxLmfLQzsn
-----END CERTIFICATE REQUEST-----

# base64 encode the csr
cat jane.csr | base64 | tr -d "\n"

# Next create a Certificate Signing Request YAML file
See (certificates/jane-csr.yaml)

# send the csr to kubernetes
kubectl apply -f jane-csr.yaml

# view the csr
kubectl get csr

# approve the csr
kubectl certificate approve jane

# view the cert
kubectl get csr jane -o yaml

# base64 decode certificate
echo "LS0...Qo=" | base64 --decode
</pre>

## Kube Config
kubectl by default looks for a $HOME/.kube/config file to pass the server, client-key, client-certificate, and certificate-authority as parameters.  If this file doesn't exist you'd have to pass these items as parameters each time such as this...
<pre>
# curl example
curl https://my-kube-playground:6443/api/v1/pods \
--key admin.key \
--cert admin.crt \
--cacert ca.crt

# kubectl example
kubectl get pods \
--server my-kube-playground:6443 \
--client-key admin.key \
--client-certificate admin.crt \
--certificate-authority ca.crt

# if the kubeconf file is somewhere other than ~/.kube/config you can pass it to kubectl
kubectl get pods --kubeconfig /path/config
</pre>

### Kube Config Contexts
You may want to change between kubeconfig contexts or clusters / users
<pre>
# view current kube config
kubectl config view
kubectl config view --kubeconfig=my-custom-config
</pre>

## Roles & Role Bindings
https://kubernetes.io/docs/reference/access-authn-authz/rbac/
<pre>
kubectl get roles
kubectl get rolebindings
kubectl describe role developer
kubectl describe rolebinding devuser-developer-binding

kubectl create -f devuser-developer-binding.yaml
</pre>

Testing access
<pre>
kubectl auth can-i create deployments
kubectl auth can-i delete nodes

kubectl auth can-i create deployments --as dev-user
kubectl auth can-i create pods --as dev-user
kubectl auth can-i create pods --as dev-user --namespace test
</pre>

Create or Edit Roles & role bindings
<pre>
kubectl create role developer --verb=list,create,delete --resources=pods
kubectl create rolebinding dev-user-binding --role=developer --user=dev-user

kubectl edit role developer -n blue
</pre>

## Namespaced vs Cluster Scoped
to view a full list of namespace and cluster scoped items use the api-resources function
<pre>
kubectl api-resources --namespaced=true
kubectl api-resources --namespaced=false
</pre>

## Service Accounts
<pre>
kubectl create serviceaccount dashboard-sa
kubectl get serviceaccount
kubectl describe serviceaccount dashboard-sa
kubectl describe secret dashboard-sa-token-kbbdm
kubectl create token dashboard-sa
</pre>

## creating a private repo
<pre>
docker login private-registory.io
docker run private-registry.io/apps/internal-app

kubectl create secret docker-registry regcred \
--docker-server= private-registry.io \
--docker-username= registry-user \
--docker-password= registry-password \
--docker-email= registry-user@org.com

spec:
  containers:
  - name: nginx
    image: private-registry.io/apps/internal-app
  imagePullSecrets:
  - name: regcred
</pre>

## Network Policies
<pre>
kubectl get networkpolicies
kubectl get netpol
kubectl 
</pre>

## Tool to quickly switch between cluster contexts and namespaces
https://github.com/ahmetb/kubectx

## Volumes
To map volumes to the local storage you can just use the volumes container see (volumes/volume-pod.yaml).  Additionally you can use AWS EBS using the block of code:
<pre>
volumes:
- name: data-volume
  awsElasticBlockStore:
    volumeID: {volume-id}
    fsType: ext4
</pre>

## Persistent Volumes
See (volumes/pv-definition.yaml)
<pre>
kubectl create -f pv-definition.yaml
kubectl get persistentvolume
</pre>

## Persistent Volume Claims
See (volumes/pvc-definition.yaml)</br>
By default persistent volumes are set to be Retained, you can modify this using the persistentVolumeReclaimPolicy:
<pre>
kubectrl get persistentvolumeclaim
kubectrl get pvc

kubectl create -f pvc-definition.yaml

kubectl delete persistentvolumeclaim myclaim
kubectl delete pvc myclaim
</pre>

## Storage Classes
If you were to use PVC's for a cloud provider like Google Cloud, you'd have to manually create the volume every time.  This is why Storage Classes exist.  They allow for dynamically provisioning storage.  You don't need a PV when using Storage Classes. See (volumes/sc-definition.yaml) and (volumes/pvc-sc-definition.yaml)
<pre>
kubectl get sc
kubectl get storageclasses
</pre>

## Linux Networking Basics
<pre>
# System A Routing
route
ip route add 192.168.2.0/24 via 192.168.1.1

# System B Routing
route
ip route add 192.168.1.0/24 via 192.168.2.1

# Default Route To Internet
ip route add default via 192.168.2.1

# Allowing forwarding packets between eth0, eth1, etc. interfaces
cat /proc/sys/net/ipv4/ip_forward
# 0 - no forward
# 1 - forwarding allowed
echo 1 > /proc/sys/net/ipv4/ip_forward

# for permenant changes modify /etc/sysctl.conf
net.ipv4.ip_forward = 1

# list and modify interfaces on the host
ip link

# list the ip addresses assigned to interfaces
ip addr

# set ip addresses on the interfaces
ip addr add 192.168.1.10/24 dev eth0

# To persist changes update the /etc/network/interfaces file

# view routing table
ip route

# list network interfaces
ifconfig

# local dns lookup (host files)
/etc/hosts
192.168.1.11    db

# find local host name
hostname

# find dns server
cat /etc/resolv.conf

# to switch order of local host files vs dns you can modify this file. You can also put other name servers in
nameserver  8.8.8.8

# you can add domain name resolutions here as well to resolve web to web.company.local
search company.local

# more commands
nslookup
dig
</pre>

## Linux Namespaces
namespaces in linux allow you to isolate networks such as containers from the main OS
<pre>
# create a new namespace
ip netns add red
ip netns add blue
ip netns

# run commands to the separate namespace
ip link   # root
ip netns exec red ip link   # red namespace
ip -n red link   # same as above

arp
ip netns exec red arp

# if you want to link to separate virtual namespaces to one other...
ip link add veth-red type veth peer name veth-blue   # create virtual link route
iplink set veth-red netns red   # assign virtual nic to a namespace red
iplink set veth-blue netns blue # assign virtual nic to a namespace blue
ip -n red addr add 192.168.15.1 dev veth-red   # assign red vnic an ip
ip -n blue addr add 192.168.15.2 dev veth-blue   # assign blue vnic an ip
ip -n red link set veth-red up   # bring up the red vnic
ip -n blue link set veth-blue up   # bring up the blue vnic
ip netns exec red ping 192.168.15.2   # test connectivity from red to blue
ip netns exec red arp   # sees the blue ip in it's history
ip netns exec blue arp   # sees the red ip in it's history
</pre>

The above works well when only connecting a few componenets to one another, but if you need to connect several you'll need to setup a virtual switch.  there are several options in linux such as Linux Bridge
<pre>
# creates a virtual network interface to be used as the virtual switch
ip link addr v-net-0 type bridge   # creates a vitual switch
ip link   # shows the virtual eth
iplink set dev v-net-0 up   # brings up the switch

# now point the existing namespace vnics to this new virtual switch interface for routing between devices
ip -n red link del veth-red   # deletes the veth-red link route
ip link add veth-red type veth peer name veth-red-br   # creates a vritual bridge nic to connect to vswitch
ip link add veth-blue type veth peer name veth-blue-br  # creates a virtual bridge nic to connect to vswitch
ip link set veth-red netns red   # assign virtual nic to a namespace red
ip link set veth-red-br master v-net-0   # assigns the virtual nic to the virtual switch
ip link set veth-blue netns blue   # assign virtual nic to a namespace blue
ip link set veth-blue-br master v-net-0   # assigns the virtual nic to the virtual switch
ip -n red addr add 192.168.15.1 dev veth-red   # assign red vnic an ip
ip -n blue addr add 192.168.15.2 dev veth-blue   # assign blue vnic an ip
ip -n red link set veth-red up   # bring up the red vnic
ip -n blue link set veth-blue up   # bring up the blue vnic

# if you'd like to connect the host server to the private networks you can add an ip to the virtual swtich
ip addr add 192.168.15.5/24 dev v-net-0   # assigns ip to vswitch to route traffic from host server to virtual namespace resources

# connecting virtual networks to an external network outside the host... First create a route from the virtual nic to the virtual switch, then create a nat rule on the local host
ip netns exec blue ip route add 192.168.1.0/24 via 192.168.15.5   # route on virtual nic
iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE   # creates nat rule to use the host as the routing
ip netns exec blue ping 192.168.1.3   # allows pinging from vnic blue namespace to external ip in another subnet

# connecting vnic namespace to the internet add another route to the namespace
ip netns exec blue ip route add default via 192.168.15.5
ip netns exec blue ping 8.8.8.8

# connecting from external network to internal vnic namespace, you can create a portforwarding rule on the host
iptables -t nat -A PREROUTING --dport 80 --to-destination 192.168.15.2:80 -j DNAT
</pre>

## Docker Networking
There are 3 modes of networks for docker
* none - no network connectivity
* host - shares the same ports as the local host
* bridge - uses the virtual switch described in linux networking
<pre>
docker network ls

ip link
</pre>

## Kubernetes Networking
<pre>
ifconfig -a
cat /etc/network/interfaces
ifconfig eth0
ip link
ip route
route

# find the port a pod is running on
netstat -natulp | grep kube-scheduler

# run a command on a remote node and return it to the local server
ssh node01 ifconfig eth0

# ports etcd is listening on
netstat -anp | grep etcd
netstat -natulp | grep etcd | grep LISTEN
</pre>

## CNI files
kubelet you can see this info
<pre>
--network-plugin=cin
--cni-bin-dir=/opt/cni/bin
--cni-conf-dir=/etc/cni/net.d

ps -aux | grep kubelet

# executables
ls /opt/cni/bin

# configs
ls /etc/cni/net.d
</pre>

## IPAM
This is the responsibility of the CNI Plugin
<pre>
cat /etc/cni/net.d/net-script.conf

"ipam": {
  "type": "host-local",
  "subnet": "10.244.0.0/16",
  "routes": [
    { "dst": "0.0.0.0/0" }
  ]
}
</pre>

Weave uses 10.32.0.0/12

## Services Networking
kube-proxy is the controller that manages services.  It does this through iptables to forward traffic from one IP to another IP.
<pre>
# you can view the service cluster ip range in kube-api-server
kube-api-server --service-cluster-ip-range ipNet (Default: 10.0.0.0/24)
ps aux | grep kube-api-server

# you can view the nat rule on the host here
iptables -L -t nat | grep db-service

# you can view the changes in the kube-proxy log
cat /var/log/kube-proxy.log
</pre>

## DNS
DNS follows the following hierarchy
<pre>
# service
service_name.namespace.svc.cluster.local
web-service.apps.svc.cluster.local
nginx.default.svc.cluster.local

# pods - not enabled by default also doesn't use pod name, uses ip address with - instead of .
10-244-2-5.namespace.pod.cluster.local
10-244-2-5.apps.pod.cluster.local
10-244-2-6.default.pod.cluster.local
</pre>

## CoreDNS
<pre>
./Coredns
cat /etc/coredns/Corefile

configuration is passed using a config map to core dns
kubectl get configmaps -n kube-system coredns -o yaml
kubectl get pods -n kube-system coredns-... -o yaml
</pre>

## Ingress
Ingress requires an ingress controller such as istio or nginx-ingress-controller.
<pre>
kubectl get ingress
kubectl get ingressclasses
kubectl get all -A | grep ingress

kubectl create ingress ingress-pay -n critical-space --rule="/pay=pay-service:8080"

kubectl expose deploy ingress-controller -n ingress-space --name ingress --port=80 --target=80 --type NodePort

# ingress redirect annotations under metadata.annotations
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
</pre>

## Installing Kubernetes with Kubeadm
If for some reason you lose the post install copy commands with your token, you can re-create one with kubeadm to join a node to a cluster using the following command
<pre>
kubeadm token create --print-join-command
</pre>

## Troubleshooting Tips
App Troubleshooting
<pre>
# save typing by creating an alias to type k instead of kubectl
alias k=kubectl

# set the current namespace to speed things up
kubectl config set-context --current --namespace=alpha

# start from top to bottom or bottom to top
check port on host
check 1st service
check 1st tier app/deployment
check 1st tier secrets/configmaps
check 2nd tier service
check 2nd tier app/deployment
check 2nd tier secrets/configmaps
</pre>

## Control Plane Troubleshooting
<pre>
# stacked topology
kubectl get nodes
kubectl get pods
kubectl get pods -n kube-system

# if installed as services...
service kube-apiserver status
service kube-controller-manager status
service kube-scheduler status
service kubelet status
service kube-proxy status

# check the logs for stacked topology
kubectl logs kube-apiserver-master -n kube-system

# logs for services
sudo journalctl -u kube-apiserver
</pre>

## Worker Node Troubleshooting
<pre>
kubectl get nodes
kubectl describe node worker-1

# cpu check
top

# disck space check
df -h

# status of the kubelet service
service kubelet status

# kubelet logs
sudo journalctl -u kubelet

# check certificates
openssl x509 -in /var/lib/kubelet/worker-1.crt -text
</pre>