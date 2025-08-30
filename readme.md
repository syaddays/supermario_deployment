Super Mario Game Deployment on AWS EKS using Terraform
Author: Syad Safi
<br/>
GitHub: syaddays
<br/>
Portfolio: syaddays.github.io

🚀 Project Overview
This project documents the end-to-end process of deploying a containerized Super Mario web game onto a managed Kubernetes cluster (AWS EKS). The entire cloud infrastructure is defined and provisioned automatically using Terraform, demonstrating the principles of Infrastructure as Code (IaC).

The process involves setting up a control environment, provisioning the cloud resources, deploying the application, and troubleshooting common configuration issues.

💻 Technologies Used
Cloud Provider: AWS (EC2, EKS, VPC, S3, IAM)

Infrastructure as Code: Terraform

Container Orchestration: Kubernetes (kubectl)

Containerization: Docker

CI/CD Tools: Git, GitHub, Bash

🛠️ Step-by-Step Deployment Guide
This guide is a direct reflection of the command-line history used to complete the project.

Step 1: Prepare the Control Environment
An AWS EC2 instance (Ubuntu) was provisioned and configured with the necessary CLI tools to manage the infrastructure and application deployment.

# Install Terraform
wget -O- [https://apt.releases.hashicorp.com/gpg](https://apt.releases.hashicorp.com/gpg) | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] [https://apt.releases.hashicorp.com](https://apt.releases.hashicorp.com) $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Install AWS CLI
curl "[https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip](https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip)" -o "awscliv2.zip"
apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install

# Install Kubectl (Kubernetes CLI)
curl -LO [https://dl.k8s.io/release/$(curl](https://dl.k8s.io/release/$(curl) -L -s [https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl](https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl)
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

Step 2: Provision AWS EKS Cluster with Terraform
With the tools installed, the Terraform configuration files were cloned and executed to build the AWS infrastructure.

# Clone the project repository
mkdir supermario
cd supermario/
git clone [https://github.com/akshu20791/supermario-game](https://github.com/akshu20791/supermario-game)
cd supermario-game/EKS-TF/

# Configure the Terraform backend (backend.tf) to use a unique S3 bucket
# This file was edited manually with 'vi'
vi backend.tf

# Initialize Terraform to download the required providers
terraform init

# Validate the Terraform configuration
terraform validate

# Preview the infrastructure changes
terraform plan

# Apply the configuration to build the AWS resources.
# This step was run multiple times after editing main.tf to fix subnet issues.
terraform apply --auto-approve

Note: During this phase, the main.tf file was edited (vi main.tf) to correct errors related to Availability Zones and subnet configurations, which is a common part of the IaC development cycle.

Step 3: Deploy the Super Mario Application to EKS
Once the cluster was ACTIVE, kubectl was used to connect to it and deploy the game from the provided Kubernetes manifest files.

# Configure kubectl to communicate with the newly created EKS cluster
aws eks update-kubeconfig --name EKS_CLOUD --region us-east-1

# Navigate to the root directory containing the Kubernetes manifests
cd ..

# Apply the deployment manifest to create the game pods
kubectl apply -f deployment.yaml

# Apply the service manifest to expose the game via a public Load Balancer
kubectl apply -f service.yaml

Step 4: Access and Verify the Game
Finally, we confirmed the deployment was successful and retrieved the public URL.

# List all running Kubernetes resources to check their status
kubectl get all

# Describe the service to find the public-facing LoadBalancer Ingress URL
kubectl describe service mario-service

The LoadBalancer Ingress URL was then copied from the output and opened in a web browser to play the game.

🧹 Clean Up
To avoid ongoing AWS charges, all resources can be destroyed using Terraform.

# Navigate back to the Terraform directory
cd EKS-TF/

# Destroy all infrastructure managed by Terraform
terraform destroy --auto-approve