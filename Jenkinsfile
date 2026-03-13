// pipeline {
//     agent any

//     environment {
//         DOCKERHUB_CREDS = credentials('dockerhub-credentials')
//         IMAGE_NAME = "pm16/ead-todo-app:latest"
//     }

//     stages {
//         stage('1. Checkout Code') {
//             steps {
//                 echo 'Pulling the latest code from GitHub...'
//                 checkout scm
//             }
//         }

//         stage('2. Build') {
//             steps {
//                 // Navigating to the appropriate folder to install dependencies
//                 dir('TODO/todo_backend') {
//                     echo 'Installing npm dependencies...'
//                     sh 'npm install'
//                 }
//             }
//         }

//         stage('3. Containerize') {
//             steps {
//                 dir('TODO/todo_backend') {
//                     echo 'Building the Docker image...'
//                     sh "docker build -t ${IMAGE_NAME} ."
//                 }
//             }
//         }

//         stage('4. Push to DockerHub') {
//             steps {
//                 echo 'Logging into DockerHub...'
//                 sh "echo \$DOCKERHUB_CREDS_PSW | docker login -u \$DOCKERHUB_CREDS_USR --password-stdin"
                
//                 echo 'Pushing image to repository...'
//                 sh "docker push ${IMAGE_NAME}"

//                 sh "docker logout"
//             }
//         }
//     }
// }

pipeline {
    agent any

    tools {
        nodejs 'node' 
    }

    environment {
        IMAGE_NAME = "pm16/ead-todo-app:latest" 
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
                // 2. Fixes the "ERROR: dockerhub-credentials" crash
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DHUB_PASS', usernameVariable: 'DHUB_USER')]) {
                    
                    echo 'Logging into Docker Hub...'
                    sh 'echo "$DHUB_PASS" | docker login -u "$DHUB_USER" --password-stdin'
                    
                    echo 'Pushing image...'
                    sh "docker push ${IMAGE_NAME}"
                }
                
                // Clean up
                sh "docker logout"
            }
        }
    }
}