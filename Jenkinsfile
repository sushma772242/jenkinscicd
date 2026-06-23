pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/sushma772242/jenkinscicd.git'
        AWS_REGION = 'eu-north-1'
        ECR_REPO_NAME = 'test-project1'
        ECR_REPO_URI = '292832245076.dkr.ecr.eu-north-1.amazonaws.com/test-project1'
        IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '292832245076'
        IMAGE_URI = "${ECR_REPO_URI}:${IMAGE_TAG}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: "${GIT_REPO}", branch: 'main'
            }
        }

        stage('Check Tools') {
            steps {
                sh '''
                    echo "Checking installed tools..."
                    aws --version
                    docker --version
                    mvn --version
                '''
            }
        }

       stage('Login to AWS ECR') {
    steps {
        sh '''
            echo "Logging into AWS ECR..."

            aws ecr get-login-password --region ${AWS_REGION} \
            | docker login --username AWS --password-stdin \
            ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
        '''
    }
}

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker image..."
                    docker build -t ${IMAGE_URI} .
                '''
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                sh '''
                    echo "Pushing Docker image to ECR..."
                    docker push ${IMAGE_URI}
                '''
            }
        }
    }

    post {
        success {
            echo "Docker image pushed to ECR successfully."
        }

        failure {
            echo "Pipeline failed."
        }
    }
}
