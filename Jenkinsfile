pipeline {
    agent any

    environment {
        REGISTRY_USER   = "varda100" 
        BACKEND_IMAGE   = "todo-backend"
        FRONTEND_IMAGE  = "todo-frontend"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker Images...'
                bat 'docker build -t %REGISTRY_USER%/%BACKEND_IMAGE%:latest ./backend'
                bat 'docker build -t %REGISTRY_USER%/%FRONTEND_IMAGE%:latest ./frontend'
            }
        }

        stage('Deploy to Docker Hub') {
            steps {
                echo 'Connecting securely to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    
                    // התחברות בטוחה ללא תקלות ווינדוס
                    bat "echo %PASS% | docker login -u %USER% --password-stdin"
                    
                    echo 'Pushing Images...'
                    bat "docker push %REGISTRY_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker push %REGISTRY_USER%/%FRONTEND_IMAGE%:latest"
                }
            }
        }

        stage('Run Containers') {
            steps {
                echo 'Deploying and running system...'
                // מנקים קונטיינרים קיימים בכוח לפני הרצה חדשה
                bat 'docker rm -f backend-container frontend-container || echo "No containers to remove"'
                bat 'docker-compose up -d'
            }
        }
    }

    post {
        failure {
            echo '❌ Pipeline failed. Please check the logs above.'
        }
    }
}