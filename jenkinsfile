pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "novrian6/php_docker_app_image"  // Docker image name
        DOCKER_TAG = "v1.0"  // Docker image version tag
        APP_PORT = "8093"  // Target port for the application
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo "Cloning repository from GitHub..."
                    git url: 'https://github.com/novrian6/php_docker_app.git', branch: 'master'

                    echo "Listing workspace contents for verification..."
                    sh 'ls -la'  // Verify that the repository is cloned correctly
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"

                    // Debug to ensure Dockerfile exists in the correct directory
                    sh 'ls -la'  // Verify the Dockerfile exists

                    // Build the Docker image
                    sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    echo "Logging into Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: 'aac33f96-6d58-447f-8ef2-7c71697acc6e', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                            echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        """
                    }
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    echo "Pushing Docker image to Docker Hub: ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
                }
            }
        }

        stage('Test Application URL') {
            steps {
                script {
                    echo "Running the Docker container on port ${APP_PORT} for testing..."
                    sh "docker run -d -p ${APP_PORT}:80 --name php_docker_app_pipeline_test ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"

                    echo "Waiting for the container to start..."
                    sleep(10)

                    echo "Testing the application URL: http://localhost:${APP_PORT}"
                    def response = sh(script: "curl -s -o /dev/null -w '%{http_code}' http://localhost:${APP_PORT}", returnStdout: true).trim()
                    
                    if (response == "200") {
                        echo "Application is up and running on http://localhost:${APP_PORT}"
                    } else {
                        error "Application failed to start. HTTP response code: ${response}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}
