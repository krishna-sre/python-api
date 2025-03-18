#!/bin/bash

terraform init
terraform plan
terraform apply -refresh=false -auto-approve

if [ $? -eq 0 ]; then
  echo "Terraform Applied Successful"
else
  echo "Terraform Apply Failed"
  exit 1 
fi



AWS_REGION="us-east-1"
ECR_REPO="$(terraform output -raw ecr_repo_url)/flask-app-repo"
IMAGE_NAME="flask-app"
IMAGE_TAG="latest"
CLUSTER_NAME="flask-app-cluster"
SERVICE_NAME="flask-app-service"
CONTAINER_PORT=5000

docker build -t $IMAGE_NAME .

docker tag $IMAGE_NAME:latest $ECR_REPO:$IMAGE_TAG

echo "Logging in to AWS ECR"
#generate temp password for login
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO

docker push $ECR_REPO:$IMAGE_TAG

echo "Triggering new ECS deployment"
aws ecs update-service \
  --cluster $CLUSTER_NAME \
  --service $SERVICE_NAME \
  --force-new-deployment \
  --region $AWS_REGION

echo "Waiting for deployment"
sleep 30

ALB_DNS=$(aws elbv2 describe-load-balancers --region $AWS_REGION \
  --names ${IMAGE_NAME}-alb \
  --query "LoadBalancers[0].DNSName" --output text)

#health checks
STATUS=$(curl -s -o /dev/null -w "%{http_code}" http://$ALB_DNS/)

if [ $STATUS -eq 200 ]; then
  echo "Health Check Passed! App is running at: http://$ALB_DNS/"
else
  echo "Health Check Failed. Status code: $STATUS"
  exit 1
fi
