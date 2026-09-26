<img width="1365" height="540" alt="image" src="https://github.com/user-attachments/assets/2d045f54-d60f-4ce1-8dcd-9536f9de5ce2" /># StreamingApp — Kubernetes Container Orchestration & Scaling

          A multi-service MERN/Streaming application containerized with Docker, packaged with Helm, deployed on Amazon EKS, exposed through an AWS Application Load Balancer, and verified with scaling, rolling updates, live chat, video upload/playback, and Kubernetes self-healing.

# 1. Project workflow

GitHub Repository - Fork and clone the StreamingApp project.

Docker Images - Build and tag five application images.

AWS ECR - Create repositories and push images.

AWS EKS Cluster - Create the Kubernetes cluster using eksctl.

Jenkins CI Pipeline  - Automate image builds and ECR pushes.

Kubernetes Manifests - Configure Deployments, Services, ConfigMaps, Secrets, and MongoDB.

Helm Chart - Package the entire application for installation and upgrades.

Ingress - Route application traffic using one external host.

Scaling and Validation - Test replicas, rolling updates, health probes, and application functionality.

Monitoring and Documentation - Configure CloudWatch 

# 2. Install required tools

Tool					      Purpose

Git				        Source code management

GitHub				    Source repository

Docker Desktop		Build container images

AWS CLI				    Access AWS

kubectl 			    Manage Kubernetes

eksctl				    Create EKS clusters

Helm 3				    Package and deploy the app
 
Jenkins				    CI pipeline

# 3. Build Docker Images

You should identify Dockerfiles for:

Auth service
Streaming service
Admin service
Chat service
Frontend

The frontend uses a multi-stage Node.js → Nginx build according to the assignment.

**Build the five Docker images**

docker build -t rajeshrajamanickam/streaming-auth:1.0.0 ` backend/authService
docker build -t rajeshrajamanickam/streaming-stream:1.0.0 ` -f backend/streamingService/Dockerfile backend
docker build -t rajeshrajamanickam/streaming-admin:1.0.0 ` -f backend/adminService/Dockerfile backend
docker build -t rajeshrajamanickam/streaming-chat:1.0.0 ` -f backend/chatService/Dockerfile backend
docker build -t rajeshrajamanickam/streaming-frontend:1.0.0 ` frontend

<img width="1017" height="242" alt="image" src="https://github.com/user-attachments/assets/4150e52c-90e8-411b-acb5-321e069e9563" />

<img width="745" height="254" alt="image" src="https://github.com/user-attachments/assets/8aef49a3-6f66-4fc3-877b-df1e4e1c896e" />

<img width="1365" height="482" alt="image" src="https://github.com/user-attachments/assets/aea8bbd5-0c13-40f6-80cb-c036eed0594c" />

# 4. AWS Setup and Amazon ECR

**Configure AWS CLI**

Configure your AWS credentials:

 - > aws configure

**Verify your identity:**

<img width="558" height="170" alt="image" src="https://github.com/user-attachments/assets/bb531803-4009-4f1f-b0b6-8a0d87c61d13" />

**Security:** Never commit AWS access keys or secret keys to GitHub. Prefer temporary credentials or an appropriate IAM role where available.

**Create ECR repositories**

The graded project requires one dedicated ECR repository per component

aws ecr create-repository `  --repository-name streaming-auth `  --region us-east-1
aws ecr create-repository `  --repository-name streaming-stream `   --region us-east-1
aws ecr create-repository `  --repository-name streaming-admin `  --region us-east-1
aws ecr create-repository `  --repository-name streaming-chat `  --region us-east-1
aws ecr create-repository `  --repository-name streaming-frontend `  --region us-east-1

**Verify repositories**

aws ecr describe-repositories `  --region us-east-1

<img width="1365" height="540" alt="image" src="https://github.com/user-attachments/assets/5af2ae2c-fa2c-48d6-a546-341753a5e029" />

# 5. Create the EKS Cluster

Create an EKS configuration file

<img width="821" height="251" alt="image" src="https://github.com/user-attachments/assets/36ebc3ad-7622-4dea-bcab-6b29b934228f" />

<img width="1365" height="311" alt="image" src="https://github.com/user-attachments/assets/6f66e97b-709f-4623-b1d9-96b8ff0f86c4" />

<img width="1365" height="401" alt="image" src="https://github.com/user-attachments/assets/b99e7e2a-d459-45fe-9bea-5187e6108daa" />

<img width="701" height="81" alt="image" src="https://github.com/user-attachments/assets/865d3213-9972-4fab-8fda-a7045d536410" />

# 6. Install required Jenkins plugins

**Common pipeline dependencies include:**

Pipeline
Git
Docker-related integration as needed
AWS credentials or AWS integration as needed

Plugin availability and installation permissions depend on the academic Jenkins setup.

**Create a Jenkins pipeline**

Example pipeline structure:

Checkout GitHub
      |
      v
Build frontend image
      |
      v
Build backend images
      |
      v
Run application tests
      |
      v
Authenticate to ECR
      |
      v
Push all images to ECR
      |
      v
Trigger deployment / update image tags

<img width="709" height="162" alt="image" src="https://github.com/user-attachments/assets/1eac8525-0fc6-43d3-bc2b-56ab6dceab74" />

<img width="1308" height="676" alt="image" src="https://github.com/user-attachments/assets/02979c2f-5515-4e01-86b9-32ef47dff181" />

<img width="1121" height="368" alt="image" src="https://github.com/user-attachments/assets/30e2b0f6-06b6-43a4-ab4d-81d31b1dc582" />

<img width="1169" height="348" alt="image" src="https://github.com/user-attachments/assets/123498ab-4049-4812-8bf1-49e97d71c4ab" />

<img width="1349" height="346" alt="image" src="https://github.com/user-attachments/assets/2d0c84d4-da48-4c24-9a11-1d6be023993b" />

<img width="1365" height="375" alt="image" src="https://github.com/user-attachments/assets/8de42a88-87d5-4971-8eec-0a5acc9833b9" />

<img width="1359" height="447" alt="image" src="https://github.com/user-attachments/assets/b1862f2e-d502-4d1e-84c6-7911aed53c83" />



# 7. Kubernetes Manifests

<img width="1277" height="559" alt="image" src="https://github.com/user-attachments/assets/de21fe72-dcf2-43af-a19f-6d51b72e98d1" />

<img width="302" height="278" alt="image" src="https://github.com/user-attachments/assets/0c012167-6a96-4ba5-b459-ff859ccfefd4" />

Each Deployment should include:

Correct image and tag
Replica count
Resource requests and limits
Readiness probe
Liveness probe
Rolling update strategy

# 8. Create Helm Chart

This generates a default chart. Remove or replace the generated templates as appropriate.

Target structure:

streamingapp/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── auth-deployment.yaml
    ├── auth-service.yaml
    ├── streaming-deployment.yaml
    ├── streaming-service.yaml
    ├── admin-deployment.yaml
    ├── chat-deployment.yaml
    ├── frontend-deployment.yaml
    ├── mongo-statefulset.yaml
    ├── configmap.yaml
    ├── secret.yaml
    └── ingress.yaml

<img width="1017" height="121" alt="image" src="https://github.com/user-attachments/assets/7a532250-1ade-4d04-91ad-5d2067ad5739" />

<img width="868" height="219" alt="image" src="https://github.com/user-attachments/assets/2e6b621b-bdbc-4f65-bb9e-270bb20ede62" />

<img width="979" height="148" alt="image" src="https://github.com/user-attachments/assets/ec9b9009-2746-4ace-ad3a-7c86d7569d31" />

<img width="886" height="265" alt="image" src="https://github.com/user-attachments/assets/5e8a72b9-8305-4a30-8192-4eea5e06269b" />

# 9. Ingress

    ingress provides one external entry point for the application.

Required routing:

/                  → frontend-svc:80
/api               → auth:3001
/api/streaming     → streaming-svc:3002
/api/admin         → admin-svc:3003
/api/chat          → chat-svc:3004
/socket.io         → chat-svc:3004

<img width="1008" height="57" alt="image" src="https://github.com/user-attachments/assets/74982d9a-7e05-4b7b-9e7c-c650c646e10d" />

# 10. Scaling

The project requires demonstrating Kubernetes scaling.

Check current replicas:

kubectl get deployment -n streamingapp

Scale the streaming service:

kubectl scale deployment/streaming \
--replicas=4 \
-n streamingapp

<img width="679" height="92" alt="image" src="https://github.com/user-attachments/assets/bd5565ac-c779-4e55-b596-d823f1f30470" />

<img width="725" height="157" alt="image" src="https://github.com/user-attachments/assets/f8180a53-9bcf-4f2b-a689-1703f7a8f877" />

<img width="886" height="265" alt="image" src="https://github.com/user-attachments/assets/b6568861-2025-45cc-b335-ee535fd54723" />

<img width="636" height="167" alt="image" src="https://github.com/user-attachments/assets/2b0891a7-0870-4069-9865-777cbbabecd8" />

<img width="639" height="85" alt="image" src="https://github.com/user-attachments/assets/26b5062c-722a-4369-8c8e-132c171b10b8" />

<img width="708" height="164" alt="image" src="https://github.com/user-attachments/assets/bb2a180f-bbd7-45d6-8698-1f0ae5a2d6e4" />


# 11. Application Smoke Tests

<img width="858" height="530" alt="image" src="https://github.com/user-attachments/assets/8722e721-81bc-4a7e-ba51-0faa4149b2b1" />

<img width="1260" height="648" alt="image" src="https://github.com/user-attachments/assets/f304631a-63e4-4ba0-8069-19d03fe01dee" />

# 12. Cleanup

<img width="626" height="72" alt="image" src="https://github.com/user-attachments/assets/dc95aa73-e6de-4fc0-96d6-9e78a5e2eab2" />

<img width="998" height="303" alt="image" src="https://github.com/user-attachments/assets/1f6d70b2-5ca8-41a1-bdd7-9866bbb0c4cd" />



# 12. Cloud watch 

<img width="742" height="529" alt="image" src="https://github.com/user-attachments/assets/58846aaf-5fd5-42e6-94ed-7f24c94acbd0" />



