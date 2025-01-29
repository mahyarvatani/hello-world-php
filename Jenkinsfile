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
                    git url: 'https://github.com/mahyarvatani/hello-world-php.git', branch: 'main'

                    echo "Listing workspace contents for verification..."
                    sh 'ls -la'  // Verify that the repository is cloned correctly
                }
            }
        }
     stage('Build') {
         steps {
             script {
                        docker.image('php:8.2-cli').inside("-v /var/jenkins_mount:/workspace") {
                        sh """
                        # Install Composer as root
                        whoami
                        php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
                        php composer-setup.php --install-dir=/usr/local/bin --filename=composer
                        php -r "unlink('composer-setup.php');"
                        
                        # Change ownership to the Jenkins user for further steps
                        chown -R jenkins:jenkins /workspace
                        
                        # Run composer install
                        cd /workspace/php_code
                        composer install
                        """
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
