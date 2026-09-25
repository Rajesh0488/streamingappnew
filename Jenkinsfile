pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '856862064332'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        IMAGE_TAG = '1.0.0'
        APP_BASE_URL = 'https://imreading.xyz'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                    docker build -t ${ECR_REGISTRY}/streaming-auth:${IMAGE_TAG} \
                      backend/authService

                    docker build -t ${ECR_REGISTRY}/streaming-stream:${IMAGE_TAG} \
                      -f backend/streamingService/Dockerfile backend

                    docker build -t ${ECR_REGISTRY}/streaming-admin:${IMAGE_TAG} \
                      -f backend/adminService/Dockerfile backend

                    docker build -t ${ECR_REGISTRY}/streaming-chat:${IMAGE_TAG} \
                      -f backend/chatService/Dockerfile backend

                    docker build \
                      --build-arg REACT_APP_AUTH_API_URL=${APP_BASE_URL}/api \
                      --build-arg REACT_APP_STREAMING_API_URL=${APP_BASE_URL}/api \
                      --build-arg REACT_APP_STREAMING_PUBLIC_URL=${APP_BASE_URL} \
                      --build-arg REACT_APP_ADMIN_API_URL=${APP_BASE_URL}/api/admin \
                      --build-arg REACT_APP_CHAT_API_URL=${APP_BASE_URL}/api/chat \
                      --build-arg REACT_APP_CHAT_SOCKET_URL=${APP_BASE_URL} \
                      -t ${ECR_REGISTRY}/streaming-frontend:${IMAGE_TAG} \
                      frontend
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'aws-ecr-creds',
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )]) {
                    sh '''
                        unset AWS_SESSION_TOKEN AWS_PROFILE AWS_DEFAULT_PROFILE
                        aws sts get-caller-identity --query Account --output text
                        aws ecr get-login-password --region "$AWS_REGION" \
                          | docker login --username AWS --password-stdin "$ECR_REGISTRY"

                        for repository in streaming-auth streaming-stream streaming-admin streaming-chat streaming-frontend; do
                          aws ecr describe-repositories --repository-names "$repository" --region "$AWS_REGION" >/dev/null
                          docker push "$ECR_REGISTRY/$repository:$IMAGE_TAG"
                        done
                    '''
                }
            }
        }
    }
}
