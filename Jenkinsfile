pipeline {
    agent any
    environment {
        IMAGE_NAME = 'mahakantil10/my-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_BIN = 'C:\\Users\\mycom\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
    }
    stages {
        stage('1. Pull Code') {
            steps {
                git branch: 'main', url: 'https://github.com/mahakantil/kub-jen.git'
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
                withCredentials([usernamePassword(credentialsId: 'kub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "if not exist \"%WORKSPACE%\\.docker\" mkdir \"%WORKSPACE%\\.docker\""
                    bat "echo {\"credsStore\":\"\"} > \"%WORKSPACE%\\.docker\\config.json\""
                    bat "echo %DOCKER_PASS%| \"${DOCKER_BIN}\" --config \"%WORKSPACE%\\.docker\" login -u %DOCKER_USER% --password-stdin"
                    bat "\"${DOCKER_BIN}\" --config \"%WORKSPACE%\\.docker\" push %IMAGE_NAME%:%IMAGE_TAG%"
                    bat "\"${DOCKER_BIN}\" --config \"%WORKSPACE%\\.docker\" push %IMAGE_NAME%:latest"
                }
            }
        }
        stage('4. Deploy to Kubernetes') {
            steps {
                bat "kubectl set image deployment/my-app-deployment my-app-container=%IMAGE_NAME%:%IMAGE_TAG%"
                bat "kubectl rollout status deployment/my-app-deployment"
            }
        }
    }
}