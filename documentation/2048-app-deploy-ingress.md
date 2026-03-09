# 2048 Game Deployment and Ingress Setup

This document covers deploying the 2048 game application on EKS and exposing it using an ALB Ingress.

## 2048 App Deploy

## Create Fargate profile
```bash
eksctl create fargateprofile \
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
```

## Deploy the deployment
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```
