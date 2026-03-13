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

        stage('4. Push') {
            steps {
                // Safely injecting credentials only when needed
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DHUB_PASS', usernameVariable: 'DHUB_USER')]) {
                    
                    echo 'Logging into Docker Hub...'
                    sh 'echo "$DHUB_PASS" | docker login -u "$DHUB_USER" --password-stdin'
                    
                    echo 'Pushing image...'
                    sh "docker push ${IMAGE_NAME}"
                }
                
                sh "docker logout"
            }
        }
    }
}