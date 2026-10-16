pipeline {
    agent any

    environment {
        APP_NAME = 'python-app'
        REPO_URL = "https://github.com/mohamedsala7320-sudo/CI-CD-repo.git"
    }

    stages {
        stage('Getting Repo files') {
            steps {
                git branch: "main", url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat """
                        docker build -t ${APP_NAME}:${BUILD_NUMBER} .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        bat """
                            echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                            docker tag ${APP_NAME}:${BUILD_NUMBER} %DOCKER_USERNAME%/${APP_NAME}:${BUILD_NUMBER}
                            docker push %DOCKER_USERNAME%/${APP_NAME}:${BUILD_NUMBER}
                        """
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        bat """
                            kubectl apply -f K8s/deployment.yaml
                            kubectl apply -f K8s/service.yaml
                            kubectl get pods
                            kubectl get services
                        """
                    }
                }
            }
        }
    }
}