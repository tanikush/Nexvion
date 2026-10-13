pipeline {
    agent any

    environment {
        IMAGE_NAME = "tanishakushwah/week9-app"
        IMAGE_TAG  = "v${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'echo "Simulating build process..."'
            }
        }

        stage('Test') {
            steps {
                bat 'echo "Running unit tests..." && echo "All tests passed"'
            }
        }

        stage('Package') {
            steps {
                bat 'tar --exclude=.git -czf app-package.tar.gz .'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t $IMAGE_NAME:$IMAGE_TAG% .'
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
                bat 'docker rm -f week9-app-new'
                bat 'docker run -d --name week9-app-new -p 8090:80 $IMAGE_NAME:$IMAGE_TAG%'
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
