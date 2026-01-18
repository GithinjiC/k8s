### Vagrant

```bash
vagrant status
vagrant up
vagrant ssh <node_name>
```

**On all nodes**

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# check the version
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

kubeadm version

sudo systemctl enable --now kubelet # Optional

# Contaier runtime
sudo apt update
sudo apt install -y containerd

# cgroups drivers. Default systemd
ps -p 1 # check default cgroup driver
sudo mkdir /etc/containerd

containerd config default # generate the default configs
containerd config default | sed 's/SystemdCgroup = false/SystemdCgroup = true/' | sudo tee /etc/containerd/config.toml
cat /etc/containerd/config.toml | grep -i SystemdCgroup -B 30
sudo systemctl restart containerd

# Kubernetes CNIs rely on Linux bridge and iptables integration. The br_netfilter module allows iptables to inspect bridged traffic, which is essential for pod-to-pod networking, service routing, and network policy enforcement.
sudo modprobe br_netfilter
ls /proc/sys/net/bridge/
sudo sysctl -w net.bridge.bridge-nf-call-iptables=1
sudo sysctl -w net.bridge.bridge-nf-call-ip6tables=1
sudo sysctl -w net.ipv4.ip_forward=1
sysctl net.bridge.bridge-nf-call-iptables

sudo tee /etc/sysctl.d/k8s-net.conf <<EOF
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system # Apply
# Verify. Output of each command should be 1
sysctl net.bridge.bridge-nf-call-iptables
sysctl net.ipv4.ip_forward
```

**Initialize the controlplane node**

```bash
# Add alias and autocomplete to .bashrc
alias k=kubectl
source /etc/bash_completion
source <(kubectl completion bash)
complete -o default -F __start_kubectl k

ip add # get the controlplane ip
sudo kubeadm init --apiserver-advertise-address <master_node_ip> --pod-network-cidr "10.244.0.0/16" --upload-certs

# from the output
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Install pod network plugin e.g flannel.
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

# Clean flannel restart
kubectl rollout restart daemonset kube-flannel-ds -n kube-flannel
```

**On the worker nodes**

```bash
# Run this from the kubeadm init on the master node to join the worker nodes to the cluster
sudo kubeadm join 192.168.100.72:6443 --token mj90wt.riacn3dbenkrumk2 \
	--discovery-token-ca-cert-hash sha256:e34c8e5d89f67346b4782e9c02f3858cf8324376fc5c9b77c8cada9783dc52fb

# command to view pods in the worker node
sudo kubectl get pods --kubeconfig=/etc/kubernetes/kubelet.conf --field-selector spec.nodeName=node02
```
