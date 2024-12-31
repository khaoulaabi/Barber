pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-barber-app"
        DOCKER_REGISTRY = "khaoulakha/repohub"  // Replace with your Docker Hub repository
        REMOTE_SERVER = "172.29.224.1"
        REMOTE_USER = "a"  // SSH username
        SONARQUBE_SCANNER_HOME = tool name: 'SonarQubeScanner', type: 'ToolLocation'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your Git repository
                git 'https://github.com/khaoulaabi/Barber.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using the Dockerfile in the repository
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Run the Docker container to serve the application
                    sh 'docker run -d -p 80:80 $DOCKER_IMAGE'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    sh "docker push $DOCKER_REGISTRY"
                }
            }
        }

        stage('Deploy to Remote Server') {
            steps {
                script {
                    // Deploy the app to the remote server via SSH
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_SERVER 'docker pull $DOCKER_REGISTRY && docker run -d -p 80:80 $DOCKER_REGISTRY'
                    """
                }
            }
        }

        stage('Clean up') {
            steps {
                script {
                    // Stop and remove the container after testing (optional)
                    sh 'docker stop $(docker ps -q --filter "ancestor=$DOCKER_IMAGE")'
                    sh 'docker rm $(docker ps -a -q --filter "ancestor=$DOCKER_IMAGE")'
                }
            }
        }
    }
}
