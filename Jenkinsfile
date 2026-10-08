pipeline {
    agent any
    environment {
        DOCKERHUB_CRED = credentials('kub-cred')
        IMAGE_NAME = 'YOUR_DOCKERHUB_USERNAME/my-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('1. Pull Code') {
            steps {
                git branch: 'main', url: 'https://github.com/mahakantil/kub-jen.git'
            }
        }
        stage('2. Build Docker Image') {
            steps {
                bat "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                bat "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }
        stage('3. Push Image to DockerHub') {
            steps {
                bat "echo %DOCKERHUB_CRED_PSW% | docker login -u %DOCKERHUB_CRED_USR% --password-stdin"
                bat "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                bat "docker push ${IMAGE_NAME}:latest"
            }
        }
        stage('4 & 5. Deploy & Restart Pods') {
            steps {
                bat "kubectl set image deployment/my-app-deployment my-app-container=${IMAGE_NAME}:${IMAGE_TAG}"
                bat "kubectl rollout status deployment/my-app-deployment"
            }
        }
    }
}