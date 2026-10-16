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
                    sh """
                        docker build -t ${APP_NAME}:${BUILD_NUMBER} .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                            echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                            docker tag ${APP_NAME}:${BUILD_NUMBER} ${DOCKER_USERNAME}/${APP_NAME}:${BUILD_NUMBER}
                            docker push ${DOCKER_USERNAME}/${APP_NAME}:${BUILD_NUMBER}
                        """
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                            # 1. تحديث اسم الصورة ورقم الـ Build في ملف الـ Deployment
                            sed -i 's|image:.*|image: ${DOCKER_USERNAME}/${APP_NAME}:${BUILD_NUMBER}|g' K8s/deployment.yaml

                            # 2. تطبيق التعديلات على Minikube
                            kubectl apply -f K8s/deployment.yaml
                            kubectl apply -f K8s/service.yaml

                            # 3. عرض حالة الـ Pods والـ Services
                            kubectl get pods
                            kubectl get services
                        """
                    }
                }
            }
        }
    }
}