Project Summary: Brain Tasks App - Full DevOps Pipeline

Task 1: Application Setup

Cloned the repository from GitHub: https://github.com/Vennilavanguvi/Brain-Tasks-App.git
Discovered the app is a static HTML application (no package.json)
App files included: index.html, vite.svg, and assets/ folder

Task 2: Dockerization

Identified app as static HTML — used Nginx Alpine as base image instead of Node.js
Created Dockerfile with:

Nginx serving files from /usr/share/nginx/html
Custom Nginx config to serve on port 3000
Built Docker image: brain-tasks-app:latest
Ran container with docker run -d -p 3000:3000
Verified app running at http://localhost:3000

Task 3: AWS ECR Registry

Configured AWS CLI with credentials using aws configure
Created ECR repository: brain-tasks-app
Authenticated Docker to ECR using aws ecr get-login-password
Tagged image with ECR URI format:
<account-id>.dkr.ecr.us-east-1.amazonaws.com/brain-tasks-app:latest
Pushed Docker image to ECR
Verified image in ECR console

Task 4: Kubernetes on AWS EKS

Installed eksctl and kubectl tools
Created EKS cluster:
Cluster name: brain-tasks-cluster
Node type: t3.medium
Node count: 2
Region: us-east-1

Updated kubeconfig: aws eks update-kubeconfig
Created deployment.yaml with:
2 replicas
ECR image reference
Resource requests and limits

Created service.yaml with:
Type: LoadBalancer
Port 80 → Container port 3000

Applied YAML files using kubectl apply
Retrieved LoadBalancer external DNS for submission

Task 5: AWS CodeBuild

Pushed all code to GitHub repository
Created buildspec.yml with 4 phases:

Install: Download and install kubectl
Pre-build: ECR login, set image tag from commit hash
Build: Docker build and tag image
Post-build: Push to ECR, update kubeconfig, deploy to EKS

Created IAM Role CodeBuildEKSRole with policies:
AmazonEC2ContainerRegistryFullAccess
AmazonEKSClusterPolicy
AmazonEKSWorkerNodePolicy
CloudWatchLogsFullAccess
AmazonS3FullAccess

Mapped CodeBuild IAM role to EKS using eksctl create iamidentitymapping
Created CodeBuild project:
Source: GitHub
Environment: Ubuntu, Standard 7.0
Privileged mode: Enabled (for Docker)
Buildspec: buildspec.yml
Logs: CloudWatch enabled

Successfully ran build and deployed to EKS

Task 6: AWS CodePipeline

Created pipeline: brain-tasks-pipeline
Connected 3 stages:

Source: GitHub (Version 2) with webhooks
Build: AWS CodeBuild brain-tasks-build
Deploy: EKS via CodeBuild
Pipeline auto-triggers on every git push to main branch
