# Ubuntu server

Complete article: [https://vitux.com/install-and-deploy-kubernetes-on-ubuntu/](https://vitux.com/install-and-deploy-kubernetes-on-ubuntu/)

### Step 1: Install Docker on both the nodes
>`sudo apt install docker.io`  
#### check correct install with: `docker --version`

### Step 2: Enable Docker on both the nodes
>`sudo systemctl enable docker` 

### Step 3: Add the Kubernetes signing key on both the nodes
>`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add`  
#### if curl does not exist: `sudo apt install curl`


### Step 4: Add Xenial Kubernetes Repository on both the nodes
>`sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"`

### Step 5: Install Kubeadm
>`sudo apt install kubeadm`
#### Verify correct installation with: `kubeadm version` 

----
All steps one command:
```shell
sudo apt install docker.io && \
sudo systemctl enable docker && \
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add && \
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main" && \
sudo apt install kubeadm && \
docker --version && \
kubeadm version 
```


## Kubernetes Deployment
### Step 1: Disable swap memory (if running) on both the nodes
>`sudo swapoff -a`  

### Step 2: Give Unique hostnames to each node
The nodes must have unique name, change it if necessary with this command: `sudo hostnamectl set-hostname <hostname-here>`


### Step3: Initialize Kubernetes on the master node
>`sudo kubeadm init --pod-network-cidr=10.244.0.0/16`
#### to obtain join command for nodes: `kubeadm token create --print-join-command`


### Step 4: Deploy a Pod Network through the master node
>`sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml` 

#### get node status with: `kubectl get pods --all-namespaces`
#### check check status of pod network: `kubectl get pods --all-namespaces`

### Install Dashboard
chek last stable version on [https://github.com/kubernetes/dashboard](https://github.com/kubernetes/dashboard)
>`sudo kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml`

latest version (even betas) [https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)
>`sudo kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta4/aio/deploy/recommended.yaml`

#### Create service account:
create admin-user.yaml
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

create admin-user-clusterrolebinding.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
>`kubectl apply -f admin-user.yaml`  
>`kubectl apply -f admin-user-clusterrolebinding.yaml` 



#### start proxy for connection:
>`kubectl proxy --address='0.0.0.0' --disable-filter=true &`  
http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/ 

##### Create token for access:
>`kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')`




### Remove node

retrieve list nodes from master: `kubectl get nodes`

#### from master:
```shell
node=<nodename> 
kubectl drain $node --delete-local-data --force --ignore-daemonsets 
kubectl delete node $node
```

#### from node:
```shell
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube* && \
sudo apt-get autoremove && \
sudo rm -rf ~/.kube
```


#### Remove docker
To identify what installed package you have: `dpkg -l | grep -i docker`
```shell
sudo apt-get purge -y docker-engine docker docker.io docker-ce && \
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce && \
sudo rm -rf /var/lib/docker /etc/docker && \
sudo rm /etc/apparmor.d/docker && \
sudo groupdel docker && \
sudo rm -rf /var/run/docker.sock
```