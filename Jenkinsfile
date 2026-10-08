pipeline {
    agent any
    environment {
        // Yahan dockerhub-credentials ko badal kar kub-cred kar dein
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
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }
        stage('3. Push Image to DockerHub') {
            steps {
                sh "echo $DOCKERHUB_CRED_PSW | docker login -u $DOCKERHUB_CRED_USR --password-stdin"
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
        stage('4 & 5. Deploy & Restart Pods') {
            steps {
                sh "kubectl set image deployment/my-app-deployment my-app-container=${IMAGE_NAME}:${IMAGE_TAG}"
                sh "kubectl rollout status deployment/my-app-deployment"
            }
        }
    }
}