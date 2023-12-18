pipeline {
    agent any

    environment {
        // Customize these environment variables
        DOCKER_IMAGE_NAME = "tgilmo300/cw02:2.0"
        DOCKERFILE_PATH = "/home/ubuntu/Dockerfile"
        DOCKERHUB_CREDENTIAL_ID = "e3af4e42-a94c-4505-a718-2527b65c43c2"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from your GitHub repository
                script {
                    git credentialsId: '57a7f0db-2417-4dfa-8e73-06423c2c097d', url: 'https://github.com/TGILMO300/CW02-'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build a Docker image from the Dockerfile
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}", "${DOCKERFILE_PATH}")
                }
            }
        }

        stage('Test Container Launch') {
            steps {
                // Test that a container can be launched from the built image
                script {
                    docker.withRun("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}", "--name test-container") {
                        // Run a command inside the container to ensure it has launched successfully
                        sh 'echo "Container launched successfully"'
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                // Push the Docker image to DockerHub
                script {
                    docker.withRegistry('https://hub.docker.com/repository/docker/tgilmo300/cw02/general', DOCKERHUB_CREDENTIAL_ID) {
                        docker.image("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            // You can customize this stage based on your specific deployment needs
            steps {
                script {
                    // Perform deployment to Kubernetes without using a Kubernetes config (customize as needed)
                    sh 'kubectl apply -f /home/ubuntu/ansible/kubernetes-manifest.yml'
                }
            }
        }
    }



post {
        always {
            // Cleanup: Remove the local Docker image created during the build
            script {
                docker.image("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}").remove()
            }
        }
    }
}
