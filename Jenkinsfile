pipeline {
    agent any

    environment {
        IMAGE_NAME = "demo-app"
        DOCKERHUB_REPO = "arfath8315/demo-app"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Package') {
            steps {
                sh '''
                  mvn clean package -DskipTests
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                  docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: 'Docker-Creds', url: '') {
                    sh '''
                      docker tag ${IMAGE_NAME}:latest ${DOCKERHUB_REPO}:latest
                      docker push ${DOCKERHUB_REPO}:latest
                    '''
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                  docker compose down || true
                  docker compose up -d --build
                '''
            }
        }
    }

    post {
        success {
            echo "Application built, pushed, and deployed successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}


