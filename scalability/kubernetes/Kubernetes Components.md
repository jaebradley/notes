# [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)

* A Kubernetes cluster consists of a set of worker machines called nodes
* Nodes run containerized applications
* Every cluster has at least one worker node
* The worker node(s) host the pods that are the components of the application workload
* The control plane manages the worker nodes and the Pods in the cluster
* In production environments, the control plane usually runs across multiple computers and a cluster usually runs multiple nodes, providing fault tolerance and high availability

## Control Plane Components

* Control plance components make global decisions about the cluster as well as detecting and responding to cluster events
* Scripts typically start all control plane components on the same machine and do not run user containers on this machine

### `kube-apiserver`

* Control plane component that exposes the Kubernetes API
* Scales by deploying more instances, balancing traffic between instances

### `etcd`

* Consistent, highly-available key/value store used for all cluster data

### `kube-scheduler`

* Control plane component that watches for newly created pods with no assigned node and selects a node for them to run
* Takes into account foctors like individual and collective resource requirements, hardware/software/policy constraints, data locality, inter-workload interference, and deadlines

### `kube-controller-manager`

* Control plane component that runs controller processes
* Each controller is a separate process, but all controllers are compiled into a single binary and run in a single process
* Node controller is responsible for noticing and responding when nodes go down
* Job controller watches for job objects and creates pods to run those tasks

## Node Components

### `kubelet`

* An agent that runs on each node in the cluster, making sure that containers are running in a pod
* Takes a set of PodSpecs that are provided and ensures that the containers described in those PodSpecs are running and healthy

### `kube-proxy`

* Network proxy that runs on each node in the cluster
* Maintains network rules on nodes, which allows network communication to pods from network sessions inside or outside the cluster
