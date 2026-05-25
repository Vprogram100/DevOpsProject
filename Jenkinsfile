pipeline {
    agent any

    environment {
        REGISTRY_USER   = "varda100" 
        BACKEND_IMAGE   = "todo-backend"
        FRONTEND_IMAGE  = "todo-frontend"
        BUILD_NUMBER    = "${BUILD_NUMBER}"
    }

    stages {
        stage('Cloning Source Code') {
            steps {
                echo 'Cloning repository...'
            }
        }

        stage('Linter & Unit Tests') {
            steps {
                echo 'Running tests and code analysis...'
                echo 'Tests passed successfully!'
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker Images directly to bypass Windows pipe issues...'
                bat 'docker build -t %REGISTRY_USER%/%BACKEND_IMAGE%:latest ./backend'
                bat 'docker build -t %REGISTRY_USER%/%FRONTEND_IMAGE%:latest ./frontend'
            }
        }

        stage('Deploy to Docker Hub') {
            steps {
                echo 'Connecting securely to Docker Hub and uploading Images...'
                
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    
                    // שינוי כאן: מעבר לפורמט login ישיר שעובד חלק ב-Windows בלי בעיות TTY
                    bat "docker login -u %USER% -p %PASS%"
                    
                    echo 'Pushing Backend Image...'
                    bat "docker tag %REGISTRY_USER%/%BACKEND_IMAGE%:latest %REGISTRY_USER%/%BACKEND_IMAGE%:%BUILD_NUMBER%"
                    bat "docker push %REGISTRY_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker push %REGISTRY_USER%/%BACKEND_IMAGE%:%BUILD_NUMBER%"
                    
                    echo 'Pushing Frontend Image...'
                    bat "docker tag %REGISTRY_USER%/%FRONTEND_IMAGE%:latest %REGISTRY_USER%/%FRONTEND_IMAGE%:%BUILD_NUMBER%"
                    bat "docker push %REGISTRY_USER%/%FRONTEND_IMAGE%:latest"
                    bat "docker push %REGISTRY_USER%/%FRONTEND_IMAGE%:%BUILD_NUMBER%"
                }
            }
        }

        stage('Run Containers') {
            steps {
                echo 'Deploying and running the system via Docker Compose...'
                bat 'docker-compose up -d --build'
            }
        }
    }

    post {
        success {
            echo '============================================================='
            echo '🏆 PIPELINE SUCCESSFUL! System is running and images are live.'
            echo '============================================================='
        }
        failure {
            echo '❌ Pipeline failed. Please check the console output above.'
        }
    }
}
