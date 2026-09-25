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

         stage('Push Images to ECR') {
            steps {
                withAWS(
                    credentials: 'aws-ecr-credsstreaming-raje',
                    region: 'ap-south-1'
                ) {
                    sh '''
                        set -e

                        echo "===== Tagging Auth Image ====="
                        docker tag \
                            streaming-auth:${IMAGE_TAG} \
                            ${ECR_REGISTRY}/streaming-auth:${IMAGE_TAG}

                        echo "===== Pushing Auth Image ====="
                        docker push \
                            ${ECR_REGISTRY}/streaming-auth:${IMAGE_TAG}


                        echo "===== Tagging Streaming Image ====="
                        docker tag \
                            streaming-stream:${IMAGE_TAG} \
                            ${ECR_REGISTRY}/streaming-stream:${IMAGE_TAG}

                        echo "===== Pushing Streaming Image ====="
                        docker push \
                            ${ECR_REGISTRY}/streaming-stream:${IMAGE_TAG}


                        echo "===== Tagging Admin Image ====="
                        docker tag \
                            streaming-admin:${IMAGE_TAG} \
                            ${ECR_REGISTRY}/streaming-admin:${IMAGE_TAG}

                        echo "===== Pushing Admin Image ====="
                        docker push \
                            ${ECR_REGISTRY}/streaming-admin:${IMAGE_TAG}


                        echo "===== Tagging Chat Image ====="
                        docker tag \
                            streaming-chat:${IMAGE_TAG} \
                            ${ECR_REGISTRY}/streaming-chat:${IMAGE_TAG}

                        echo "===== Pushing Chat Image ====="
                        docker push \
                            ${ECR_REGISTRY}/streaming-chat:${IMAGE_TAG}


                        echo "===== Tagging Frontend Image ====="
                        docker tag \
                            streaming-frontend:${IMAGE_TAG} \
                            ${ECR_REGISTRY}/streaming-frontend:${IMAGE_TAG}

                        echo "===== Pushing Frontend Image ====="
                        docker push \
                            ${ECR_REGISTRY}/streaming-frontend:${IMAGE_TAG}


                        echo "===== All Images Pushed Successfully ====="
                    '''
                }
            }
        }

    }
}

post {
        success {
            echo 'StreamingApp CI/CD pipeline completed successfully.'
        }

        failure {
            echo 'StreamingApp CI/CD pipeline failed.'
        }
    }
}