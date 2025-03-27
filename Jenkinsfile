pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/simple-web-app'
        SONARQUBE_URL = 'http://your-sonarqube-server:9000'
        SONARQUBE_TOKEN = credentials('sonar-token')  // Add this in Jenkins credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-repo/simple-web-app.git'
            }
        }

        stage('Static Code Analysis') {
            steps {
                script {
                    sh "sonar-scanner -Dsonar.host.url=${SONARQUBE_URL} -Dsonar.login=${SONARQUBE_TOKEN}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-cred', url: '']) {
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Cleanup') {
            steps {
                sh "docker rmi ${DOCKER_IMAGE}:latest"
            }
        }
    }
}
