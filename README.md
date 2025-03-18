Stages:         
Stage 1> Github Repo for Source code
Stage 2 > Flask API Dockerfile for Containerize Flask App
Stage 3 >   Bash Script:   
       -  Terraform provisioning
          - Build Docker Image       
          - Push to AWS ECR         
         - Trigger ECS Deployment   
         - Health Check            
Stage 4 >  AWS Elastic Container Registry to store docker images     
Stage 5 >  AWS ECS Cluster (Fargate)   for  Runs Flask containers
Stage 6 > Application Load Balancer  for Routes traffic, performs health checks
Stage 7 >  End Users / Clients   for Access Flask API via ALB DNS
