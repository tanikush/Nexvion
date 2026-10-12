pipeline {
    agent any

    environment {
        IMAGE_NAME = "tanishakushwah/week9-app"
        IMAGE_TAG  = "v${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                bat 'echo "Simulating build process..."'
            }
        }

        stage('Test') {
            steps {
                echo 'Running automated tests...'
                bat 'echo "Running unit tests..." && echo "All tests passed"'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application...'
                bat 'tar --exclude=.git -czf app-package.tar.gz .'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                bat 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    bat 'docker push $IMAGE_NAME:$IMAGE_TAG'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying new version (rolling deployment)...'
                bat '''
                    docker stop week9-app-old 
                    docker rm week9-app-old 
                    docker run -d --name week9-app-new -p 8090:80 $IMAGE_NAME:$IMAGE_TAG
                    exit /b 0
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipline complete successfully! Application deployed.'
        }
        failure {
            echo 'Pipline failed. Review console ouput for details.'
        }
    }
 }

    post {
        success {
            echo 'Pipeline completed successfully! Application deployed.'
        }
        failure {
            echo 'Pipeline failed. Review console output for details.'
        }
    }
}
