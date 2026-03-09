# EKS-2048-game
![AWS](https://img.shields.io/badge/AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)

## 📋 Overview
This project deploys the classic **2048 game** on **Amazon EKS** using **Fargate** profiles and an **ALB Ingress Controller** for external access.

## 🏗️ Architecture
User → ALB Ingress → Service → Pods (2048 game) → EKS Fargate

## 🛠️ Tech Stack
- **Cloud:** AWS (EKS, Fargate, IAM, EC2, VPC)
- **Orchestration:** Kubernetes, kubectl
- **Infrastructure:** eksctl, AWS CLI
- **Networking:** ALB Ingress Controller
- **Package Management:** Helm

## 🚀 Quick Start

### Prerequisites
- AWS account with IAM user
- AWS CLI configured (`aws configure`)
- `kubectl`, `eksctl`, `helm` installed

### Deploy
```bash
# Create EKS cluster
eksctl create cluster --name=demo-cluster --region=us-east-1 --fargate
```
```bash
# Install ALB Controller
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
```
```bash
eksctl create iamserviceaccount \
  --cluster=demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::<YOUR_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```
```bash
helm repo add eks https://aws.github.io/eks-charts
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```
```bash
# Deploy 2048 game
kubectl create namespace game-2048
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```

### Access the Game
```bash
kubectl get ingress -n game-2048
# Use the ADDRESS from output
```

## Screenshots
### Live Game
<img width="1900" height="988" alt="eks game 2048" src="https://github.com/user-attachments/assets/29e07586-5ff8-449f-bd9a-57bf0f4a17e3" />

### Pods Running
<img width="1599" height="285" alt="image" src="https://github.com/user-attachments/assets/1ca431ee-30a7-47cb-9c27-543ba8146a69" />

## Resource Status
```bash
NAME                                   READY   STATUS    RESTARTS   AGE
pod/deployment-2048-7bf64bccb7-8kkwf   1/1     Running   0          5h6m
pod/deployment-2048-7bf64bccb7-dsrkr   1/1     Running   0          5h6m
pod/deployment-2048-7bf64bccb7-lpd6k   1/1     Running   0          5h6m
pod/deployment-2048-7bf64bccb7-pd876   1/1     Running   0          5h6m
pod/deployment-2048-7bf64bccb7-ts7th   1/1     Running   0          5h6m

NAME                   TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/service-2048   NodePort   10.100.181.168   <none>        80:31083/TCP   5h6m

NAME                                     CLASS   HOSTS   ADDRESS                                                                  PORTS   AGE
ingress.networking.k8s.io/ingress-2048   alb     *       k8s-game2048-ingress2-bcac0b5b37-309808220.us-east-1.elb.amazonaws.com   80      5h6m

```
## Clean Up
```bash
kubectl delete namespace game-2048
eksctl delete cluster --name=demo-cluster
```

## 📚 What I Learned
- EKS cluster provisioning with Fargate </br>
- IAM Roles for Service Accounts (IRSA) </br>
- ALB Ingress Controller setup </br>
- Kubernetes deployments and services </br>
- Helm for package management </br>

## 📖 Documentation
- <a href="documentation/alb-controller-add.md">alb-controller-add.md</a> - ALB controller installation steps
- <a href="documentation/2048-app-deploy-ingress.md">2048-app-deploy-ingress.md</a> - App deployment guide

### Credits
Built while following Abhishek Veeramalla's AWS series.
