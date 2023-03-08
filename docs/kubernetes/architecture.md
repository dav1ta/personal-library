# Master Node
The master node is responsible for managing, planning, scheduling, and monitoring nodes in the cluster.

#### Kube-apiserver
The kube-apiserver is responsible for orchestrating all actions in the cluster. It is what is behind the `kubectl` command. It uses HTTP POST requests and can be installed separately as a service at `/etc/systemd/system/kube-apiserver.service`, or it can be installed automatically as a pod at `/etc/kubernetes/manifests/Kube-apiserver.yaml`. The kube-apiserver does the following:

- Authenticates the user
- Validates requests
- Retrieves data
- Updates the ETCD cluster
- Assigns a node to the request using the scheduler
- Sends the assigned node to the kubelet
- Updates the ETCD cluster with the status of the kubelet

#### ETCD Cluster
The ETCD cluster is a key-value store that is installed on the master node. It stores information about the cluster, including nodes, pods, configs, secrets, accounts, roles, bindings, and other information. It can be configured for high availability by setting up multiple instances. It is a standalone store that is not tied to any specific service.

#### Kube-scheduler
The kube-scheduler is responsible for managing the scheduling of containers on nodes. It determines which pods should be run on which nodes. It can be run as a service and uses algorithms to prioritize nodes based on available resources (such as CPU). For example, it may do the following:

- Filter nodes based on available resources
- Rank nodes using a priority algorithm on a scale of 0-10

### Controllers
Controllers are responsible for monitoring the system and ensuring that desired state is maintained. They can be downloaded as a service and run on the master node.

#### Node Controllers
Node controllers are responsible for monitoring the status of nodes and ensuring that they are running. They check the status of nodes every 5 seconds, and if a node becomes unreachable, they wait 40 seconds before marking it as unreachable.

#### Replication Controllers
Replication controllers are responsible for ensuring that the desired number of pods are running. If there are not enough pods, they will create new ones to meet the desired count.

# Worker Nodes
Worker nodes host applications as containers.

#### Container Runtime Engine
The container runtime engine is responsible for running and managing containers on the node. An example of a container runtime engine is Docker.

#### Kubelet
The kubelet is an agent that runs or creates pods on the node. It is responsible for registering the node with the cluster.

#### Kube-proxy
The kube-proxy can be run as a service and is installed on each node in the cluster. It creates iptables rules to facilitate communication between worker nodes.

# Pods
A pod is the basic execution unit in Kubernetes and is where a container lives. It is recommended to have one container per pod, but helper containers can also be deployed with the main container.
