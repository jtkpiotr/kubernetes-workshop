
# Kubernetes Cluster Setup

## Initialize Master Node
Initialize all master components:
```bash
sudo kubeadm init --apiserver-advertise-address=192.168.188.10 --pod-network-cidr=10.244.0.0/16
```

To access API via kubectl:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### Change node IP
Change node IP (Vagrant issue) (on each node set its enp0s8 ip address)
Add this line:

`Environment="KUBELET_EXTRA_ARGS=--node-ip=<nodeIP>"`

to:

`/etc/systemd/system/kubelet.service.d/10-kubeadm.conf`

and restart kubelet daemon:
```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

#### Setup Flannel Network
Apply flannel network:
```bash
kubectl apply -f kube-flannel.yml
```

## Initialize Worker Nodes
Join to the master:
```bash
sudo kubeadm join --token <TOKEN> <MASTER_IP>:6443 --discovery-token-ca-cert-hash <CERT>
```


#### Change node IP
Change node IP (Vagrant issue) (on each node set its enp0s8 ip address)
Add this line:

`Environment="KUBELET_EXTRA_ARGS=--node-ip=<nodeIP>"`

to:

`/etc/systemd/system/kubelet.service.d/10-kubeadm.conf`

and restart kubelet daemon:
```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

And that's it :) We've just created our cluster.

## Deploy application and a database
```bash
kubectl apply -f web-deployment.yml
kubectl apply -f web-service.yml
kubectl apply -f db-deployment.yml
kubectl apply -f db-service.yml
```

## Add Dashboard UI Plugin
```bash
kubectl apply -f kubernetes-dashboard.yml
kubectl apply -f dashboard-role.yml
```

Enable HTTP proxy to access Dashboard from the host machine
```bash
kubectl proxy --address=0.0.0.0 --accept-hosts='^*$'
```

Access dashboard from your host machine
```bash
http://192.168.188.10:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```

## Helpful Commands
```bash
sudo kubeadm token create --print-join-command
sudo tcpdump -pni enp0s8 tcp port 6443
sudo netstat -tp
sudo nsenter --net=/var/run/docker/netns/<NETNS> <COMMAND>
ip -d link show
```
