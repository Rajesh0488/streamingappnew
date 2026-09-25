pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '856862064332'
        ECR_REGISTRY = "856862064332.dkr.ecr.us-east-1.amazonaws.com"

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
                    docker build -t streaming-auth:1.0.0 \
                      backend/authService

                    docker build -t streaming-stream:1.0.0 \
                      -f backend/streamingService/Dockerfile backend

                    docker build -t streaming-admin:1.0.0 \
                      -f backend/adminService/Dockerfile backend

                    docker build -t streaming-chat:1.0.0 \
                      -f backend/chatService/Dockerfile backend

                    docker build -t streaming-frontend:1.0.0 \
                      frontend
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                // Authenticate and push using Jenkins-managed AWS credentials.
                echo 'Configure ECR push steps for your Jenkins environment.'
            }
        }
    }
}
