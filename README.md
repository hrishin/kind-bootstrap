# kind-bootstrap

Local Kubernetes bootstrap for coding challenges, using [kind](https://kind.sigs.k8s.io/).

It spins up a cluster with:
- 1 master and 2 workers with different containerd and kubelet versions ([1.17](./nodes/Dockerfile.17), [1.18](./nodes/Dockerfile.18))
- one of the worker nodes has a _"/sensitive"_ filesystem mount
- worker nodes also support the runsc runtime ([gVisor](https://gvisor.dev/))

## Setup

### Install Kind and docker
1) [Docker](https://docs.docker.com/get-docker/) 
2) [kind](https://github.com/kubernetes-sigs/kind/releases)

### Set up cluster
```bash
$ git clone https://github.com/MarshallWace/kind-bootstrap.git
$ cd kind-bootstrap/
```
There are 2 clusters configurations
* [cluster-1.yaml](./cluster-1.yaml)
* [cluster-2.yaml](./cluster-2.yaml)

Both clusters have 3 nodes. The only change in the `cluster-2` is, one worker node's kubelet patch version ahead compare to other nodes. Where as in the `cluster-1`, 2 nodes are having the same kubelet version.


### Use cluster-1:
```bash
$ kind create cluster --config cluster-1.yaml --name mw
Creating cluster "mw" ...
Creating cluster "mw" ...
 ✓ Ensuring node image (ghcr.io/marshallwace/kind-node:17) 🖼 
 ✓ Ensuring node image (ghcr.io/marshallwace/kind-node:18) 🖼 
 ✓ Ensuring node image (kindest/node:v1.18.8) 🖼 
 ✓ Preparing nodes 📦 📦 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
 ✓ Joining worker nodes 🚜 
Set kubectl context to "kind-mw"
You can now use your cluster with:

kubectl cluster-info --context kind-mw

Thanks for using kind! 😊
```
```bash
$ kubectl get nodes --context kind-mw
NAME               STATUS   ROLES    AGE     VERSION
mw-control-plane   Ready    master   2m30s   v1.18.8
mw-worker          Ready    <none>   101s    v1.17.5
mw-worker2         Ready    <none>   101s    v1.18.8
```

### Use cluster-2: 

Its configured to test the wrap pods in gVisor scenario.

```bash
$ kind create cluster --config cluster-2.yaml --name mw
Creating cluster "mw" ...
 ✓ Ensuring node image (ghcr.io/marshallwace/kind-node:17) 🖼 
 ✓ Ensuring node image (ghcr.io/marshallwace/kind-node:18) 🖼 
 ✓ Ensuring node image (kindest/node:v1.18.6) 🖼 
 ✓ Preparing nodes 📦 📦 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
 ✓ Joining worker nodes 🚜 
Set kubectl context to "kind-mw"
You can now use your cluster with:

kubectl cluster-info --context kind-mw

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/
```
```bash
$ kubectl get nodes --context kind-mw
NAME               STATUS   ROLES    AGE   VERSION
mw-control-plane   Ready    master   13m   v1.18.6
mw-worker          Ready    <none>   12m   v1.17.5
mw-worker2         Ready    <none>   12m   v1.18.8
```

## Cleanup

```bash
$ kind delete cluster --name mw
```
