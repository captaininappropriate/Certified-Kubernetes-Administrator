# High level summary of the Worker Node components

## Control Plane
Manage the overall state of the cluster:

- kube-apiserver : The core component server that exposes the Kubernetes HTTP API
- etcd : Consistent and highly-available key value store for all API server data
- kube-scheduler : Looks for Pods not yet bound to a node, and assigns each Pod to a suitable node
- kube-controller-manager : Runs controllers to implement Kubernetes API behavior
- cloud-controller-manager (optional) : Integrates with underlying cloud provider(s)

# Worker Node
Run on every node, maintaining running pods and providing the Kubernetes runtime environment:

- kubelet : Ensures that Pods are running, including their containers
- kube-proxy : Maintains network rules on nodes to implement Services
- Container runtime : Software responsible for running containers