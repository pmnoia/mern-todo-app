pipeline {
    agent any

    environment {
        IMAGE_NAME = "pm16/mern-todo-app:latest" 
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
                    echo 'Installing npm dependencies...'
                    sh 'npm install'
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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DHUB_PASS', usernameVariable: 'DHUB_USER')]) {
                    
                    echo 'Logging into Docker Hub securely...'
                    sh 'echo "$DHUB_PASS" | docker login -u "$DHUB_USER" --password-stdin'
                    
                    echo 'Pushing image to Docker Hub...'
                    sh "docker push ${IMAGE_NAME}"
                }
                
                // Clean up
                sh "docker logout"
            }
        }
    }
}