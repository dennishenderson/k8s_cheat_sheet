# ETCD
ETCD is a key value database store for linux that Kubernetes relies on to manage the cluster master and nodes.  You can find the latest download instructions [Here](https://github.com/etcd-io/etcd/releases).  You can either manually install etcd as a pre-requesite or leverage kubeadm to install it as part of the process. Etcd Default Port: 2379

## Types Of Data Stored In ETCD
<pre>
* Nodes
* PODs
* Configs
* Secrets
* Accounts
* Roles
* Bindings
* Others...
</pre>

## ETCD API Versions
There are two API versions you may need to deal with, version 2 and version 3.  To change versions use either the in-place command or change the environment varaibles
<pre>
Uses api v3 for a single command
ETCDCTL_API=3 ./etcdctl version

sets api to v3 for all future commands
export ETCDCTL_API=3
./etcdctl version
</pre>

## ETCD API v2 Commands
<pre>
./etcd
./etcdctl set key1 value1
./etcdctl get key 1
./etcdctl
./etcdctl --version

etcdctl backup
etcdctl cluster-health
etcdctl mk
etcdctl mkdir
etcdctl set
</pre>

## ETCD API v3 Commands
<pre>
./etcdctl put key1 value1
./etcdctl get key1

etcdctl snapshot save 
etcdctl endpoint health
etcdctl get
etcdctl put
</pre>