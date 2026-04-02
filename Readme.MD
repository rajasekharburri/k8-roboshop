🚀 Roboshop on Kubernetes (EKS)

This project demonstrates deploying a microservices-based Roboshop application on AWS EKS using Kubernetes manifests.

📌 Architecture Overview
AWS EKS Cluster
Microservices:
Catalogue
User
Cart
Shipping
Payment
Databases:
MongoDB
MySQL
Redis
RabbitMQ
⚙️ Prerequisites

Make sure you have:

AWS Account
EC2 Instance (Amazon Linux / RHEL)
IAM permissions for EKS
Installed tools:
Docker
kubectl
eksctl
🛠️ Setup Instructions
1️⃣ Install Docker
#!/bin/bash
growpart /dev/nvme0n1 4
lvextend -L +30G /dev/mapper/RootVG-varVol
xfs_growfs /var

dnf -y install dnf-plugins-core
dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo

dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

systemctl start docker
systemctl enable docker
usermod -aG docker ec2-user
2️⃣ Install kubectl
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.34.2/2025-11-13/bin/linux/amd64/kubectl
chmod +x kubectl
mkdir -p $HOME/bin
cp kubectl $HOME/bin/
export PATH=$HOME/bin:$PATH
3️⃣ Install eksctl
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp

sudo install -m 0755 /tmp/eksctl /usr/local/bin
4️⃣ Configure AWS
aws configure
5️⃣ Create EKS Cluster
eksctl create cluster --config-file=eks.yaml
📦 Deploy Application
Create Namespace
kubectl apply -f 01-namespace.yaml
Install kubens (optional)
sudo git clone http://github.com/ahmetb/kubectx /opt/kubectl
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens

kubens roboshop
🧩 Deploy Databases
MongoDB
kubectl apply -f mongodb/manifest.yaml
Redis
kubectl apply -f redis/manifest.yaml
MySQL
kubectl apply -f mysql/manifest.yaml
kubectl exec -it <mysql-pod> -- bash
mysql -u root -pRoboShop@1
RabbitMQ
kubectl apply -f rabbitmq/manifest.yaml
🔍 Debug Pod

Build and push debug image:

docker build -t <dockerhub-username>/debug:v1 .
docker push <dockerhub-username>/debug:v1

Run debug:

kubectl exec -it debug -- bash

Test connections:

telnet mongodb 27017
telnet rabbitmq 5672
🧱 Deploy Microservices
Catalogue
kubectl apply -f catalogue/manifest.yaml
curl http://catalogue:8080/health
User
kubectl apply -f user/manifest.yaml
Cart
kubectl apply -f cart/manifest.yaml
Shipping
kubectl apply -f shipping/manifest.yaml
curl http://cart:8080/health
Payment
kubectl apply -f payment/manifest.yaml
Frontend
kubectl apply -f frontend/manifest.yaml
📊 Verification
kubectl get pods
kubectl get svc
kubectl get namespace
🌐 Services Example Output
NAME        TYPE        PORT
catalogue   ClusterIP   8080
mongodb     ClusterIP   27017
mysql       ClusterIP   3306
rabbitmq    ClusterIP   5672
redis       ClusterIP   6379
🧪 Troubleshooting
Check Pod Logs
kubectl logs <pod-name>
Exec into Pod
kubectl exec -it <pod-name> -- bash
DNS Check
nslookup catalogue