# AWS Load Balancer Controller Installation
#### This document outlines the steps to install the AWS Load Balancer Controller on your EKS cluster, enabling the use of ALB Ingress.

## Prerequisites
- An existing EKS cluster (```demo-cluster```)
- IAM OIDC provider configured for your cluster
- ```helm``` installed locally

## Installation Steps
### 1. Download IAM Policy
```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

### 2. Create IAM Policy
```bash
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

### 3. Create IAM Role using ```eksctl```
```bash
eksctl create iamserviceaccount \
  --cluster=demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

### 4. Install Controller using Helm
#### Add the EKS Helm repository
```bash
helm repo add eks https://aws.github.io/eks-charts
```

#### Update the repository
```bash
helm repo update eks
```

#### Install the controller
```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

#### Verify Installation
```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```
