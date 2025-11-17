pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = "891377289801"
        AWS_REGION = "eu-west-1"
        ECR_REPO = "jenkins-ecs-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/PSaras12/jenkins-ecs-cicd.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $ECR_REPO:$IMAGE_TAG .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Tag and Push Image') {
            steps {
                sh '''
                docker tag $ECR_REPO:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG
                docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                aws ecs update-service \
                    --cluster jenkins-ecs-cluster \
                    --service jenkins-ecs-service \
                    --force-new-deployment \
                    --region $AWS_REGION
                '''
            }
        }
    }
}
