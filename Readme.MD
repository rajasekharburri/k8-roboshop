# 🚀 Roboshop on Kubernetes (EKS)

This project demonstrates deploying a **microservices-based Roboshop application** on **AWS EKS** using Kubernetes manifests.

---

## 📌 Architecture Overview

- AWS EKS Cluster
- Microservices:
  - Catalogue
  - User
  - Cart
  - Shipping
  - Payment
- Databases:
  - MongoDB
  - MySQL
  - Redis
  - RabbitMQ

---

## ⚙️ Prerequisites

Make sure you have:

- AWS Account
- EC2 Instance (Amazon Linux / RHEL)
- IAM permissions for EKS
- Installed tools:
  - Docker
  - kubectl
  - eksctl

---

## 🛠️ Setup Instructions

### 1️⃣ Install Docker

```bash
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
```

---

### 2️⃣ Install kubectl

```bash
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.34.2/2025-11-13/bin/linux/amd64/kubectl
chmod +x kubectl
mkdir -p $HOME/bin
cp kubectl $HOME/bin/
export PATH=$HOME/bin:$PATH
```

---

### 3️⃣ Install eksctl

```bash
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp

sudo install -m 0755 /tmp/eksctl /usr/local/bin
```

---

### 4️⃣ Configure AWS

```bash
aws configure
```

---

### 5️⃣ Create EKS Cluster

```bash
eksctl create cluster --config-file=eks.yaml
```

---

## 📦 Deploy Application

### Create Namespace

```bash
kubectl apply -f 01-namespace.yaml
```

---

### Install kubens (optional)

```bash
sudo git clone http://github.com/ahmetb/kubectx /opt/kubectl
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens

kubens roboshop
```

---

## 🧩 Deploy Databases

### MongoDB

```bash
kubectl apply -f mongodb/manifest.yaml
```

### Redis

```bash
kubectl apply -f redis/manifest.yaml
```

### MySQL

```bash
kubectl apply -f mysql/manifest.yaml
kubectl exec -it <mysql-pod> -- bash
mysql -u root -pRoboShop@1
```

### RabbitMQ

```bash
kubectl apply -f rabbitmq/manifest.yaml
```

---

## 🔍 Debug Pod

```bash
docker build -t <dockerhub-username>/debug:v1 .
docker push <dockerhub-username>/debug:v1

kubectl exec -it debug -- bash
```

Test connections:

```bash
telnet mongodb 27017
telnet rabbitmq 5672
```

---

## 🧱 Deploy Microservices

### Catalogue

```bash
kubectl apply -f catalogue/manifest.yaml
curl http://catalogue:8080/health
```

### User

```bash
kubectl apply -f user/manifest.yaml
```

### Cart

```bash
kubectl apply -f cart/manifest.yaml
```

### Shipping

```bash
kubectl apply -f shipping/manifest.yaml
curl http://cart:8080/health
```

### Payment

```bash
kubectl apply -f payment/manifest.yaml
```

### Frontend

```bash
kubectl apply -f frontend/manifest.yaml
```

---

## 📊 Verification

```bash
kubectl get pods
kubectl get svc
kubectl get namespace
```

---

## 🧪 Troubleshooting

### Logs

```bash
kubectl logs <pod-name>
```

### Exec

```bash
kubectl exec -it <pod-name> -- bash
```

### DNS Check

```bash
nslookup catalogue
```

