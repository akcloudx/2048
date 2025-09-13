# Project Cleanup 🧹

After you have finished, use the following commands to delete all the resources associated with the cluster. This is an important step to avoid incurring unnecessary AWS costs.

### 1. Delete the Cluster

This command will delete the EKS cluster and all the resources managed by `eksctl`, including the VPC, subnets, and IAM roles. This process may take several minutes to complete.

eksctl delete cluster --name demo-cluster --region us-east-1

### 2. Manual Verification in the AWS Console

After the command has finished, it is crucial to re-check the AWS Management Console to ensure no resources were left behind. This is a final safety net to prevent unexpected charges.

EKS Clusters: Navigate to the EKS service in the console to confirm that the demo-cluster is no longer listed.

CloudFormation: eksctl uses CloudFormation to manage resources. Go to the CloudFormation service and check that all stacks with eksctl in their name have been deleted.

EC2 Load Balancers: Go to the EC2 service, and check the "Load Balancers" section under the "Load Balancing" menu. Ensure the Application Load Balancer is gone. If not, delete it manually.

Fargate Profiles: Check the EKS console under your cluster's page to confirm that the Fargate profile has been removed.

IAM Roles: Check the IAM service to ensure that the roles created for the EKS cluster and the ALB controller have been deleted. Look for roles with eksctl or eks in their names.

VPC: Lastly, check the VPC service. The VPC created by eksctl should be gone. If it remains, you can delete it manually, but be sure to check for any dependencies first.
