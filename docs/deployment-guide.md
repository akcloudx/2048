# Project Deployment Guide 🚀

This document provides a step-by-step guide to deploying the serverless web application.

### 1. Create the EKS Cluster with Fargate

Use the following command to create a serverless EKS cluster. `eksctl` will automatically provision a VPC, the EKS control plane, and a Fargate profile, so you don't have to manage any worker nodes. This process may take a few minutes to complete.

eksctl create cluster --name demo-cluster --region us-east-1 --fargate

### 2. Configure IAM OIDC Provider

Next, associate an IAM OIDC provider with your cluster. This is a one-time step that enables secure authentication for Kubernetes service accounts with IAM, a crucial prerequisite for the AWS Load Balancer Controller.

eksctl utils associate-iam-oidc-provider --cluster demo-cluster --approve

### 3. Set up the AWS Load Balancer Controller 🚦

The AWS Load Balancer Controller is a Kubernetes add-on that provisions and manages AWS Application Load Balancers (ALB) and Network Load Balancers (NLB) for your cluster.

3.1. Configure IAM Permissions

This is the most critical step. We will create an IAM policy and an IAM Role for Service Account (IRSA). This grants the controller permissions to create and manage load balancers in your AWS account without storing any credentials in the pod. This step relies on the OIDC provider you configured previously.

First, download the official IAM policy:

curl -O [https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json](https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json)

Next, create the IAM policy in your AWS account using the downloaded file:
