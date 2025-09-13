# Cloud-Native Mastery: A Serverless EKS Deployment 🚀

This repository contains the infrastructure as code and configuration for deploying the classic **2048 game** to a serverless, highly-available web application on **Amazon EKS**. The project demonstrates the power of integrating Kubernetes with core AWS services like **Fargate** and the **Application Load Balancer (ALB)** to build scalable and cost-effective solutions.

### Table of Contents

1.  [Key Features & Technologies](#key-features--technologies-💡)
2.  [Project Architecture](#project-architecture-🏛️)
3.  [Prerequisites](#prerequisites-🛠️)
4.  [Getting Started: Deployment Guide](#getting-started-deployment-guide-🚀)
5.  [Project Cleanup](#project-cleanup-🧹)

### Key Features & Technologies 💡

* **Application:** The **2048 game** is deployed as the sample application.
* **AWS EKS:** Managed Kubernetes for container orchestration.
* **AWS Fargate:** Serverless compute to run containers without managing EC2 instances.
* **AWS Load Balancer Controller:** A Kubernetes add-on that automatically provisions an ALB for external access.
* **IAM Roles for Service Accounts (IRSA):** The standard, secure way to grant AWS permissions to Kubernetes pods.
* **`eksctl` & `kubectl`:** Command-line tools for simplified cluster and workload management.
* **Helm:** A package manager for installing Kubernetes applications.
* **Custom Domain:** Optional CNAME record configuration to access the application via a custom domain.

### Project Architecture 🏛️

The EKS control plane manages the cluster, while **AWS Fargate** provides the underlying compute for the pods. The **AWS Load Balancer Controller** automatically provisions an **Application Load Balancer**, routing external traffic to the application's service.


### Prerequisites 🛠️

To get started with this project, you need the following tools installed and configured on your local machine.

* **AWS CLI**: The official command-line tool for managing AWS services.
* **`kubectl`**: The standard command-line tool for interacting with Kubernetes clusters.
* **`eksctl`**: A command-line tool that simplifies the creation and management of EKS clusters.
* **Helm**: A package manager for installing and managing Kubernetes applications.

For detailed instructions on installation and configuration, refer to the [prerequisites documentation](./docs/prerequisites.md).

### Getting Started: Deployment Guide 🚀

Follow our detailed, step-by-step guide to deploy the entire project and see the application in action.

**[Start the Deployment Guide](./docs/deployment-guide.md)**

### Project Cleanup 🧹

Once you're done, remember to clean up all the resources to avoid unnecessary costs.

**[Follow the Cleanup Guide](./docs/cleanup.md)**
