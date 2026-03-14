# EKS Project Terminal Steps

This file documents all the steps executed in the terminal:




1. **AWS Configuration**

```bash
aws configure
# Enter AWS Access Key, Secret Key, region (ap-south-1)

Create EKS cluster with Fargate

eksctl create cluster --name project-cluster --region ap-south-1 --fargate

Create Fargate profiles

eksctl create fargateprofile --cluster project-cluster --name game-2048-profile --namespace game-2048

Deploy 2048 game app

kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
kubectl get pods -n game-2048 -w

Set up IAM OIDC provider for ALB controller

eksctl utils associate-iam-oidc-provider --cluster project-cluster --approve
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
eksctl create iamserviceaccount \
  --cluster=project-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::940482408917:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

Install AWS Load Balancer Controller via Helm

brew install helm
helm repo add eks https://aws.github.io/eks-charts
helm repo update
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=project-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=ap-south-1 \
  --set vpcId=vpc-0d00e3847703c2c25

Verify deployment

kubectl get deployment -n kube-system aws-load-balancer-controller
kubectl get ingress -n game-2048

---

### **3. Scripts**

#### **scripts/create-cluster.sh**
```bash
#!/bin/bash
# Create EKS cluster with Fargate
eksctl create cluster --name project-cluster --region ap-south-1 --fargate
scripts/create-fargate-profile.sh
#!/bin/bash
# Create Fargate profiles for namespaces
eksctl create fargateprofile --cluster project-cluster --name game-2048-profile --namespace game-2048
scripts/deploy-2048-app.sh
#!/bin/bash
# Deploy 2048 app
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
