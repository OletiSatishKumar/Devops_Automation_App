pipeline {
    agent any

    environment {
        BACKEND_REPO = "taskmanager-backend"
        CLIENT_REPO  = "taskmanager-frontend"
        IMAGE_TAG    = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                branch "main", "dev"
                https://github.com/OletiSatishKumar/Devops_Automation_App.git
            }
        }

        stage('Build & Tag Docker Images') {
            steps {
                withCredentials([
                    string(credentialsId: 'DOCKER_USERNAME', variable: 'DOCKER_USER'),
                    string(credentialsId: 'DOCKER_PASSWORD', variable: 'DOCKER_PASS')
                ]) {
                    script {
                        env.BACKEND_IMAGE = "${DOCKER_USER}/${BACKEND_REPO}:${IMAGE_TAG}"
                        env.CLIENT_IMAGE  = "${DOCKER_USER}/${CLIENT_REPO}:${IMAGE_TAG}"

                        dir('backend') {
                            docker.build(env.BACKEND_IMAGE)
                        }

                        dir('client') {
                            docker.build(env.CLIENT_IMAGE, "--build-arg NEXT_PUBLIC_API_URL=http://backend:8000/api/v1 .")
                        }
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([
                    string(credentialsId: 'DOCKER_USERNAME', variable: 'DOCKER_USER'),
                    string(credentialsId: 'DOCKER_PASSWORD', variable: 'DOCKER_PASS')
                ]) {
                    script {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"

                        sh "docker push ${env.BACKEND_IMAGE}"
                        sh "docker push ${env.CLIENT_IMAGE}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Docker images pushed: ${IMAGE_TAG}"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}
