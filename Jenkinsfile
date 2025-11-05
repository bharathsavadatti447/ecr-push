pipeline {
    agent any

    environment {
        GIT_REPO   = 'git@github.com:bharathsavadatti447/ecr-push.git'
        BRANCH     = 'main'
        AWS_REGION = 'us-east-1'
        ECR_REPO_NODE = 'public.ecr.aws/o9v8l7j1/dockerrepo-node'
        ECR_REPO_JAVA = 'public.ecr.aws/o9v8l7j1/dockerrepo-java'
        IMAGE_NAME_1 = 'node-app'
        IMAGE_TAG_1  = 'v1.0.0'
        IMAGE_NAME_2 = 'java-app'
        IMAGE_TAG_2  = 'v1.0'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git(
                    branch: "${BRANCH}",
                    url: "${GIT_REPO}",
                    credentialsId: 'a5e5c631-8e24-48ee-9844-ea7c8b7a658d'
                )
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    sh '''
                        echo "Logging into AWS Public ECR..."
                        aws ecr-public get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin public.ecr.aws/o9v8l7j1
                    '''
                }
            }
        }

        stage('Build Node App Docker Image') {
            steps {
                script {
                    dir('node-app') {
                        sh '''
                            echo "Building Node.js Docker image..."
                            docker build -t $IMAGE_NAME_1:$IMAGE_TAG_1 .
                            docker tag $IMAGE_NAME_1:$IMAGE_TAG_1 $ECR_REPO_NODE:$IMAGE_TAG_1
                        '''
                    }
                }
            }
        }

        stage('Build Java App Docker Image') {
            steps {
                script {
                    dir('java-app') {
                        sh '''
                            echo "Building Java Docker image..."
                            docker build -t $IMAGE_NAME_2:$IMAGE_TAG_2 .
                            docker tag $IMAGE_NAME_2:$IMAGE_TAG_2 $ECR_REPO_JAVA:$IMAGE_TAG_2
                        '''
                    }
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh '''
                        echo "Pushing Node.js Docker image to ECR..."
                        docker push $ECR_REPO_NODE:$IMAGE_TAG_1
                        
                        echo "Pushing Java Docker image to ECR..."
                        docker push $ECR_REPO_JAVA:$IMAGE_TAG_2
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Both Node and Java Docker images pushed successfully to ECR."
        }
        failure {
            echo "Failed to push Docker images to ECR."
        }
    }
}

