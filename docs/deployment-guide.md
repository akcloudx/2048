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

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

Finally, create the IAM role and associate it with a Kubernetes service account using eksctl. This command creates the link between the AWS and Kubernetes security models.

eksctl create iamserviceaccount \
    --cluster=<your-cluster-name> \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve

3.2. Deploy the Controller with Helm

Now that the permissions are in place, you can deploy the controller into your cluster using Helm.

First, add and update the official EKS Helm chart repository:

helm repo add eks [https://aws.github.io/eks-charts](https://aws.github.io/eks-charts)
helm repo update eks

Then, install the controller. The --set flags are used to configure the chart to use the resources you just created.

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
    --set clusterName=<your-cluster-name> \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set region=<your-region> \
    --set vpcId=<your-vpc-id>

3.3. Verify the Deployment

Confirm that the controller's deployment is running successfully in the kube-system namespace.

kubectl get deployment -n kube-system aws-load-balancer-controller

### 4. Deploy the 2048 Game Application 🎮

The following command uses a single YAML file to deploy a Kubernetes Deployment, Service, and Ingress resource for the 2048 game.

The Deployment creates the pods that run the game.

The Service provides a stable network endpoint for the pods.

The Ingress resource tells the AWS Load Balancer Controller to provision an Application Load Balancer to expose your game to the internet.

kubectl apply -f [https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml](https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml)

### 5. Verify & Access the Application 🌐

After you deploy the Ingress resource, the AWS Load Balancer Controller will start provisioning an Application Load Balancer (ALB). It may take a few minutes for this process to complete. You can monitor the status using one of two methods:

Method 1: Using the AWS Console

Navigate to the EC2 service in your AWS Management Console. In the left-hand navigation pane, select Load Balancers under the Load Balancing section. Look for the ALB with a name that corresponds to your Ingress. Wait for the state to change from provisioning to active. Once it's active, copy the DNS name of the load balancer.

Method 2: Using the Command Line

You can also get the DNS name directly from the command line by checking the Ingress resource. The ADDRESS field will be populated with the DNS URL once the ALB is active.

kubectl get ingress -n game-2048

Once the ADDRESS is populated, copy the DNS name.

Accessing the Application

Paste the copied DNS name into your web browser. You may need to prepend http:// to the URL. If everything is configured correctly, you will see the 2048 game running.


