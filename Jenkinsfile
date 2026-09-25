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
                    set -e
                    docker build -t "streaming-auth:$IMAGE_TAG" \
                      -f backend/authService/Dockerfile backend/authService

                    docker build -t "streaming-stream:$IMAGE_TAG" \
                      -f backend/streamingService/Dockerfile backend

                    docker build -t "streaming-admin:$IMAGE_TAG" \
                      -f backend/adminService/Dockerfile backend

                    docker build -t "streaming-chat:$IMAGE_TAG" \
                      -f backend/chatService/Dockerfile backend

                    docker build \
                      --build-arg REACT_APP_AUTH_API_URL="$APP_BASE_URL/api" \
                      --build-arg REACT_APP_STREAMING_API_URL="$APP_BASE_URL/api" \
                      --build-arg REACT_APP_STREAMING_PUBLIC_URL="$APP_BASE_URL" \
                      --build-arg REACT_APP_ADMIN_API_URL="$APP_BASE_URL/api/admin" \
                      --build-arg REACT_APP_CHAT_API_URL="$APP_BASE_URL/api/chat" \
                      --build-arg REACT_APP_CHAT_SOCKET_URL="$APP_BASE_URL" \
                      -t "streaming-frontend:$IMAGE_TAG" frontend
                '''
            }
        }

        stage('Push Images to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-ecr-credsstreaming-raje'
                ]]) {
                    sh '''
                        set -e
                        ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
                        echo "Authenticated AWS account: $ACCOUNT_ID"
                        test "$ACCOUNT_ID" = "$AWS_ACCOUNT_ID" || {
                          echo "AWS credentials do not belong to expected account $AWS_ACCOUNT_ID"
                          exit 1
                        }

                        aws ecr get-login-password --region "$AWS_REGION" \
                          | docker login --username AWS --password-stdin "$ECR_REGISTRY"

                        for repository in streaming-auth streaming-stream streaming-admin streaming-chat streaming-frontend; do
                          aws ecr describe-repositories --repository-names "$repository" --region "$AWS_REGION" >/dev/null
                          docker tag "$repository:$IMAGE_TAG" "$ECR_REGISTRY/$repository:$IMAGE_TAG"
                          docker push "$ECR_REGISTRY/$repository:$IMAGE_TAG"
                        done
                    '''
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
