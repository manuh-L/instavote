# Instavote App Deployment on Kubernetes

This guide provides instructions on how to deploy the Instavote application on Kubernetes. The deployment includes several components such as Redis, the voting UI, a worker, the result UI, and a PostgreSQL database.

## Prerequisites

- Kubernetes cluster
- kubectl configured to communicate with your cluster
- Helm installed

## Deployment Steps


Start by creating a dedicated namespace for the Instavote app:

```bash
1. Create a Namespace
kubectl create namespace ivote

2. Deploy Redis
#Deploy Redis using the provided manifests:
kubectl apply -f deploy/redis/base/deployment.yaml -n ivote
kubectl apply -f deploy/redis/base/service.yaml -n ivote

3. Deploy Vote UI
#Deploy the voting UI component:
kubectl apply -f deploy/vote/base/deployment.yaml -n ivote
kubectl apply -f deploy/vote/base/service.yaml -n ivote

4. Deploy Worker
#Deploy the worker component:
kubectl apply -f deploy/worker/base/deployment.yaml -n ivote

######################## HELM #####################################

5. Deploy Result UI with Helm
#You have two options for deploying the result UI:

Option 1: Using the Provided Chart
helm install result ./deploy/charts/result -n ivote

Option 2: Creating a New Helm Chart
#If you prefer to create a new Helm chart and configure it:
helm create result
vim values.yaml
Update the following values:

#The image repository and tag to nhiuana/result
#The service type to NodePort
#Set the image tag to master

6. Deploy PostgreSQL Database with Helm
#First, add the Helm repository that contains the PostgreSQL chart:
helm repo add lfs269 https://lfs269.github.io/helm-charts/
#Update the Helm Repository,Fetch the latest chart index from the repository:
helm repo update

#Deploy PostgreSQL
#You can deploy the PostgreSQL chart either directly with set parameters or using a custom values file:

Option 1: Using Set Parameters
helm install db lfs269/postgres --set settings.authMethod=trust,service.name=db -n ivote

Option 2: Using a Custom Values File
helm install db lfs269/postgres -f deploy/db/db.yaml -n ivote

#Verify the Deployment, Check the status of the deployed pods in the ivote namespace:
kubectl get po -n ivote
