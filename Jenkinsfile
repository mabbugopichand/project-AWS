pipeline {
    agent any  // Use any available agent

    environment {
        DOCKER_IMAGE = "gopi80/simple-webapp"
        SONAR_HOST_URL = "http://35.174.16.51:9000/"
        SONAR_PROJECT_KEY = "project-AWS"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/mabbugopichand/project-AWS.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'jenkins') {
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=$SONAR_PROJECT_KEY -Dsonar.host.url=$SONAR_HOST_URL'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 8080:80 $DOCKER_IMAGE'
            }
        }
    }
}
