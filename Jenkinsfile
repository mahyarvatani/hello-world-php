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
                        docker.image('php:8.2-fpm').inside("-u root -v /home/admin/jenkins_data/jenkins_home/workspace:/workspace") {
                        sh """
                        cd /workspace/second-pipeline
                        php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
                        php composer-setup.php
                        php -r "unlink('composer-setup.php');"
                        php composer.phar install
                        php -l index.php
                        """
                 }
            }
         }
      }
     stage('test') {
        steps {
             script {
                        docker.image('php:8.2-fpm').inside("-u root -v /home/admin/jenkins_data/jenkins_home/workspace:/workspace") {
                        sh """
                           echo 'Testing ...'
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
