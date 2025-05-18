pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ECR_REPO = "009160029390.dkr.ecr.ap-south-1.amazonaws.com/satyajit-coursevita-repo"
        IMAGE_TAG = "latest"
        IMAGE_NAME = "${ECR_REPO}:${IMAGE_TAG}"
        REMOTE_HOST = "ec2-user@13.201.124.43"
        REMOTE_APP_NAME = "httpd-app"
    }

    stages {
        stage('Checkout from GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/chinabudhi123/DevOps-Dokcer-ECR.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-jenkins-creds']]) {
                    sh """
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin $ECR_REPO
                    """
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh "docker push $IMAGE_NAME"
            }
        }

        stage('Manual Approval') {
            steps {
                input message: "✅ Approve deployment to EC2 at $REMOTE_HOST?", ok: "Deploy"
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: ['ec2-access']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_HOST '
                            aws ecr get-login-password --region $AWS_REGION | \
                            docker login --username AWS --password-stdin $ECR_REPO &&
                            docker pull $IMAGE_NAME &&
                            docker stop $REMOTE_APP_NAME || true &&
                            docker rm $REMOTE_APP_NAME || true &&
                            docker run -d --name $REMOTE_APP_NAME -p 80:80 $IMAGE_NAME
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}

