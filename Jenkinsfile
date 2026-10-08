pipeline {
    agent any
    environment {
        DOCKERHUB_CRED = credentials('kub-cred')[cite: 5]
        IMAGE_NAME = 'mahakantil/my-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_BIN = 'C:\\Users\\mycom\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
    }
    stages {
        stage('1. Pull Code from GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/mahakantil/kub-jen.git'[cite: 4, 5]
            }
        }
        stage('2. Build Docker Image') {
            steps {
                bat "\"${DOCKER_BIN}\" build -t %IMAGE_NAME%:%IMAGE_TAG% ."
                bat "\"${DOCKER_BIN}\" tag %IMAGE_NAME%:%IMAGE_TAG% %IMAGE_NAME%:latest"
            }
        }
        stage('3. Push Image to DockerHub') {
            steps {
                bat "echo %DOCKERHUB_CRED_PSW% | \"${DOCKER_BIN}\" login -u %DOCKERHUB_CRED_USR% --password-stdin"
                bat "\"${DOCKER_BIN}\" push %IMAGE_NAME%:%IMAGE_TAG%"
                bat "\"${DOCKER_BIN}\" push %IMAGE_NAME%:latest"
            }
        }
        stage('4. Deploy to Kubernetes from DockerHub') {
            steps {
                bat "kubectl set image deployment/my-app-deployment my-app-container=%IMAGE_NAME%:%IMAGE_TAG%"
                bat "kubectl rollout status deployment/my-app-deployment"
            }
        }
    }
}