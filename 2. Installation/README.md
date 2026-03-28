# Step by step installation of both the Kubernetes control plane and worker node

1. Control Plane

- Installation

```
#!/bin/bash

sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab # make permenant
# or simply delete the swap partition from /etc/fstab

sudo apt update 

# install containerd
sudo apt install -y containerd

# create default config
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# enable systemd cgroups
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# restart containerd
sudo systemctl restart containerd
sudo systemctl enable containerd

# load required kernel modules & sysctl settings
sudo modprobe overlay
sudo modprobe br_netfilter

# set set sysctl paramaters
cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

# apply settings
sudo sysctl --system

# install transport layer
sudo apt install -y curl gpg
sudo apt install -y apt-transport-https curl

# install kubernetes packages on Ubuntu
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# verify
kubeadm version
kubectl version --client
```

Follow the directions output to the terminal once the installation is complete


- Configuration

```
#!/bin/bash

sudo su -
user=<username>
pass=<password>
sudo useradd -m -d /home/$user $user
sudo echo "$user:$pass" | chpasswd
usermod -aG sudo $user

exit

ip_address=192.168.1.200
cidr=172.18.0.0/16
publicIP=192.168.1.200

sudo kubeadm init --cri-socket=unix:///run/containerd/containerd.sock --control-plane-endpoint $publicIP --apiserver-advertise-address $ip_address --pod-network-cidr=$cidr --upload-certs

# install CNI
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s-1.11.yaml
```

2. Worker Node
- Installation

```
#!/bin/bash

sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab # make permenant

sudo apt update 

# install containerd
sudo apt install -y containerd

# create default config
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# enable systemd cgroups
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# restart containerd
sudo systemctl restart containerd
sudo systemctl enable containerd

# load required kernel modules & sysctl settings
sudo modprobe overlay
sudo modprobe br_netfilter

# set set sysctl paramaters
cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

# apply settings
sudo sysctl --system

# install transport layer
sudo apt install -y curl gpg
sudo apt install -y apt-transport-https curl

# install kubernetes packages on Ubuntu
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update
sudo apt install -y kubelet kubeadm kubectl

# verify
kubeadm version
kubectl version --client
```

Using the details provided during the control plane installation join the woker node to the cluster