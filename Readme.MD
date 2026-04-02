kunernates
=========
1 install docker 
2 install kubectl
3 install eksctl
eksctl create cluster --config-file=eks.yaml
4 aws configure





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
==================================================================================================
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.34.2/2025-11-13/bin/linux/amd64/kubectl
chmod +x ./kubectl
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH

===================================================================================================

ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
sudo install -m 0755 /tmp/eksctl /usr/local/bin && rm /tmp/eksctl


kubectl apply -f 01-namespace.yaml


sudo git clone http://github.com/ahmetb/kubectx /opt/kubectl
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens
kubens roboshop

kubectl get pods
kubectl get namespace

mongodb
=======

kubectl apply -f mongodb/manifeat.yaml
kubectl get svc


debug ---used to check connections (run in non root container)

docker build -t rajasekhar1616/debug:v1 .

docker login -u rajasekhar1616    (to push ) 
docker push rajasekhar1616/debug:v1

     kubectl get pods

     kubectl exec -it debug -- bash
   telnet mongodb 27017

catalogue
========
     kubectl apply -f catalogue/manifest.yaml
       kubectl get pods



        telnet catalogue 8080
        curl http://catalogue:8080/health
        nslookup catalogue

Redis
====
          kubectl apply -f redis/manifest.yaml
MySQL
======
          kubectl apply -f mysql/manifest.yaml

          kubectl exec -it mysql-565f687886-4qxkv -- bash
          mysql -u root -pRoboShop@1
          show databases;
           show tables;

RabbitMQ
=========
       kubectl apply -f rabbitmq/manifest.yaml

       kubectl get pods

    NAME                         READY   STATUS    RESTARTS   AGE
    catalogue-7ddcb7959b-xh5x7   1/1     Running   0          28m
    debug                        1/1     Running   0          33m
    mongodb-56cf745979-kbmxl     1/1     Running   0          48m
    mysql-565f687886-4qxkv       1/1     Running   0          10m
    rabbitmq-fc657d9cc-c6vhb     1/1     Running   0          26s
    redis-6fd6569c54-682ml       1/1     Running   0          13m

kubectl get svc
NAME        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)     AGE
catalogue   ClusterIP   10.100.48.71     <none>        8080/TCP    28m
mongodb     ClusterIP   10.100.197.24    <none>        27017/TCP   48m
mysql       ClusterIP   10.100.205.230   <none>        3306/TCP    11m
rabbitmq    ClusterIP   10.100.191.186   <none>        5672/TCP    32s
redis       ClusterIP   10.100.118.252   <none>        6379/TCP    13m


kubectl exec -it debug -- bash
telnet rabbitmq 6379

user
====

         kubectl apply -f user/manifest.yaml
          kubectl get pods
         kubectl get svc



cart
======

kubectl apply -f cart/manifest.yaml

 kubectl get pods
kubectl exec -it debug -it -- bash


shipping
=========
kubectl apply -f shipping/manifest.yaml
kubectl get pods
kubectl exec -it debug -it -- bash
curl http://cart:8080/health



payment
========

kubectl apply -f payment/manifest.yaml
kubectl get pods




frontent
===========

kubectl apply -f frontent/manifest.yaml
 kubectl get pods
kubectl get svc