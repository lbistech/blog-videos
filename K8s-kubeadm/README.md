# Create a Kubernetes Lab Environment using Kubeadm from Scratch step by step
TABLE OF CONTENTS:
- Creating EC2 instances as master and worker nodes
- Installation of Kubeadm and Kubectl
- Setting up the Kubernetes cluster using Kubeadm
- Nginx application deployment on cluster using imperative and declarative methods

Commands:
- Run following commands on both master and worker nodes
 sudo su
 apt update -y
 apt install docker.io -y

 systemctl start docker
 systemctl enable docker

- Run following commands on both instances
 curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg
 echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' > /etc/apt/sources.list.d/kubernetes.list

 apt update -y
 apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y

- Initialize the kubeadm on just Master Node
 kubeadm init

- Now on Master node exit from root user and type :
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config

- Now write the below command on Worker node :
 sudo su
 kubeadm reset pre-flight checks


- Now on the Master node, type the following command to genereate the token
 kubeadm token create --print-join-command

- Paste the generated token with "--v=5" verbosity
 kubeadm join 172.31.23.249:6443 --token vt02le.tynbqp89046y1br3 --discovery-token-ca-cert-hash sha256:033d73daf2f70803dd2754691d08b0048e5ffe2abfe7a33445bbba31cca18bd8 --v=5

- Allow inbound security group with port 6443

- Master node commands:
kubectl get nodes
kubectl get nodes -o wide

- Nginx pod creation command:
 kubectl run ngnix --image=ngnix --restart=Always

- Nginx Deployment file
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
spec:
  template:
    metadata:
      name: nginx
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
          - containerPort: 80
  replicas: 1
  selector:
    matchLabels:
      app: myapp


