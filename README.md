# AWS EKS Project

This project demonstrates the setup of an AWS EKS cluster using **Fargate** and deploying a sample 2048 game application using Kubernetes and the AWS Load Balancer Controller.

## Features

- EKS Cluster creation with `eksctl`
- Fargate profiles for serverless compute
- Deployment of a sample Kubernetes app (`2048`)
- AWS Load Balancer Controller setup with IAM OIDC provider

## Folder Structure

- `iam_policy.json` → IAM policy for Load Balancer Controller
- `scripts/` → Bash scripts to automate cluster creation, Fargate profile, and app deployment
- `eks-setup.md` → Detailed log of terminal commands used
