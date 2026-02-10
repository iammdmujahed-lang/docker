pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mohammed12mujahed/amazon-nginx"
        DOCKER_TAG   = "latest"
    }

    stages {

        stage('Checkout Source Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/iammdmujahed-lang/docker.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
        success {
            echo '✅ Docker image built and pushed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}

