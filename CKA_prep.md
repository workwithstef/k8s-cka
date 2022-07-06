# Let's Learn K8s

## Basics

Main Components

- API Server (master)
- etcd (master) - distributed key-value store
- Scheduler (master) - distributes work across multiple nodes
- Controller (master) 

- Container Runtime (worker) - underlying framework to run container applications
- kubelet (worker) - agent reporting to API server


### Setting up 

Prerequisites: 

- VM hypervisor (like virtualbox)
- kubectl
- minikube

Download and install these three tools.

kubectl = command line utility to manage K8s server
minikube = local K8s environment
VM hypervisor = spins up machine minikube uses to run local K8s


### Pods

Pods - single k8s instance which contains container
Pods generally have 1:1 relationship with containers ==> one app container per pod
UNLESS app container requires 'helper' container which it is dependent on.

Key Commands:
- `kubectl run nginx --image=nginx` spins up pod, running container with 'nginx' Dockerhub image 
- `kubectl describe pod nginx` thorough description of container boot up
- `kubectl get pods` - shows active pods
- `kubectl get pods -o wide` - shows active pods + additional info
- `kubectl delete pod <pod_name>` - deletes pod
### YAML

Pod-definition

```
apiVersion: v1 						# defines pod versioning
kind: Pod
metadata: 							# metadata only takes specific child parameters as defined in k8s docs
  name: myapp-pod 
  labels:							# labels, like tags, can be defined freely as a dictionary of key-values
    app: myapp  			
	  type: front-end
spec:
  containers: 					# can have multiple containers in pod so this parameters takes a list
	- name: nginx-container
	  image: nginx

```
standard top level parameters:
- apiVersion
- kind
- metadata
- spec

Key Commands:

- `kubectl apply -f <yaml-filename>` - runs yaml file
- `kubectl run redis --image=redis --dry-run=client -o yaml > redis-pod.yaml` - creates yaml syntax for pod definition then redirects code to "redis-pod.yaml" file


### Replication Controller

High Availability benefits:

- Replication controllers spins up new pod if original fails
- spans against multiple nodes; and balances workloads
`kubectl create -f replication.yaml` - creates replication controllers

### Replica Set
Monitors labelled Pods and creates new one if one fails

- Use labels and selectors to specify which pods to monitor
- YAML file nearly completely identical to RC minus a few differences [as shown in yml file]
- replica sets can take into account replicas that already existed using "selector" key
`kubectl create -f replicaset.yaml` - creates replica sets

Scaling:

1. Change "replicas" value in .yml file
2. `kubectl replace -f replicaset.yaml` - updates replicas
OR
`kubectl scale --replicas=6 -f replicaset.yaml` - scales up replicas via cmd line
OR 
`kubectl scale --replicas=6 replicaset myapp-replicaset`

`kubectl edit replicaset myapp-replicaset` - edits active replica set directly without yaml file

### Deployments
One level above ReplicaSets.

- Creating a Deployment creates a ReplicaSet by default
- YAML definition fifle exactly identical to ReplicaSet .yml but "kind: Deployment"
`kubectl create -f deployment-defintion.yaml` - creates deployment set
`kubectl get all` - shows all k8s objects

Deployment Strategies:
- Recreate => takes down/spins up everything at once
- RollingUpdate => takes down/spins up pods one at a time



`kubectl apply -f deployment-definition.yaml` - updates replica set after changes made to deployment yml file
OR
`kubectl set image deployment myapp-deploy nginx-container=alpine` - updates using cmd line; DOES NOT update yml file


Rollouts & Rollbacks:
`kubectl rollout status deployment myapp-deploy` - shows status of current rollout
`kubectl rollout history deployment myapp-deploy` - shows history of deployment rollouts
`kubectl rollout undo deployment myapp-deploy` - rolls back deployment to previous replica set state

*Note: when creating or editing include `--record` option to record changes; shown in `history` command*

`kubectl edit deployment myapp-deploy`

### Networking

- IP addreesses are assigned to PODS
- Managing IP addresses across nodes so they dont conflict must be done manually with a k8s networking tool.
- Otherwise PODS in separate nodes may be created with the same default IP address

#### Services:
- a k8s object; like Pods, ReplicaSets,etc
- acts as a middle man between a host computer and a Pod IP - basically a Load Balancer
- like a virtual server inside the node, comes with IP address => ClusterIP
 
Service Types:
- NodePort 
- ClusterIP
- LoadBalancer

Porting:
- TargetPort = default port on the pod (80)
- Port => port on the service itself (80)
- NodePort => the port on the Node itself [30000-32767]


ClusterIP
- useful for communication between multiple microservices (app -> backend ->  database)
- not for external-facing services; speaks to apps within cluster

Load Balancer
- useful when frontend pods are on multiple nodes; so users can hit one url to reach web app

`kubectl get svc` - shows all services
`kubectl describe service` - shows full breakdown
`minikube service <service-name> --url` - prints url (if any)

*Note: with microservices application clusters always draw the setup + networking requirements to easily visualise k8s deployment approach*

#################
K8s club question:
- clarify difference between Service targetPort and Port & in which scenario would they be different values

#################

## Advanced

### ETCD 
- stores info about the cluster and all its objects. Every `kubectl get` command gets info from etcd.
Installation:
- non-kubeadm: install and configure etcd.service
- kubeadm setup: install and deploys etcd as a pod
`etcdctl` - CLI tool used to interact with etcd

### Kube API Server
- primary management component
- authenticates requests, retrieves data, responds back with info
- updates etcd
- scheduler & kubelets use api server for instructions
Installation:
- non-kubeadm: install and configure kube-apiserver.service
- non-kubeadm setup: inspect options at `/etc/systemd/system/kube-apiserver.service`


### Kube Controller Manager
- Node Controller => monitors health of nodes, reprovisions pods onto healthy nodes; as per ReplicaSet
- Replication Controller => monitors pod health, recreates if one fails as per ReplicaSet
- Many other controllers
Configuring Controller options:
- Installing kube-controller-manager, then specify options to be enabled
- kubeadm tool deploys kube-controller-manager as a pod
- non-kubeadm setup: inspect options at `/etc/systemd/system/kube-controller-manager.service`

### Scheduler
- Only decides which pod goes on which node. Kubelet actually places pods on the node
- Scheduler filters for appropriate node based on pod requirements 
Configuring Scheduler options:
- Installing kube-scheduler, then specify options to be enabled
- kubeadm tool deploys kube-scheduler as a pod
- inspect options at `/etc/kubernetes/manifest/kube-scheduler.yaml`

### Kubelet

- Registers Node with Cluster
- Creates Pods
- Monitors Pods & Nodes
Installation:
- always manually install kubelets
- kubeadm tool DOES NOT auto deploy in a pod like previous components


