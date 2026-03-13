pipeline {
    agent any

    environment {
        IMAGE_NAME = "pm16/finead-todo-app:1.0" 
    }

    stages {
        stage('1. Checkout') {
            steps {
                echo 'Pulling code from repository...'
                checkout scm
            }
        }

        stage('2. Build') {
            steps {
                dir('TODO/todo_backend') {
                    echo 'Using Docker to install npm dependencies...'
                    // This creates a temporary Node container just to run npm install!
                    sh 'docker run --rm -v "$(pwd):/app" -w /app node:22-alpine npm install'
                }
            }
        }

        stage('3. Containerise') {
            steps {
                dir('TODO/todo_backend') {
                    echo 'Building the Docker image...'
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('4. Push to DockerHub') {
            steps {
                echo 'Logging into DockerHub...'
                sh "echo \$DOCKERHUB_CREDS_PSW | docker login -u \$DOCKERHUB_CREDS_USR --password-stdin"
                
                echo 'Pushing image to repository...'
                sh "docker push ${IMAGE_NAME}"

                sh "docker logout"
            }
        }
    }
}