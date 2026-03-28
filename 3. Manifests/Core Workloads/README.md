# Core Workloads

1. Pod
    - Smallest deployable unit
    - Runs one or more containers
    - Primary container plus additional containers are known as sidecar containers
    - Sidecar containers can be used for logging, monitoring, security, or networking
2. Deployment
    - 
3. DaemonSet
    - An application deployment method that configures specific pods to run on some or all nodes within the cluster
    - Main purpose of DaemonSets is to provide control over which nodes hosts a particular pod
    - Used for cases like ensuring that a copy of a logging, monitoring or a security dagent runs on each node
4. StatefulSet
    - Lets you assign persistent identities to pods and control the order in which they start
    - Useful if you need to associate pods with stateful storage resources
5. ReplicaSet