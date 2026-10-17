pipeline {
    agent any

    environment {
        APP_NAME = 'flask-app'
        DOCKER_USER = 'mosala7320'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mohamedsala7320-sudo/CI-CD-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_USER/$APP_NAME:$BUILD_NUMBER .'
                sh 'docker tag $DOCKER_USER/$APP_NAME:$BUILD_NUMBER $DOCKER_USER/$APP_NAME:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER_VAR', passwordVariable: 'DOCKER_PASS_VAR')]) {
                    sh 'echo $DOCKER_PASS_VAR | docker login -u $DOCKER_USER_VAR --password-stdin'
                    sh 'docker push $DOCKER_USER/$APP_NAME:$BUILD_NUMBER'
                    sh 'docker push $DOCKER_USER/$APP_NAME:latest'
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
                sh 'kubectl rollout restart deployment/flask-app-deployment'
            }
        }
    }
}