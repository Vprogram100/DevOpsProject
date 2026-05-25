pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'varda100' 
        BACKEND_IMAGE   = "devopsproject-backend"
        FRONTEND_IMAGE  = "devopsproject-frontend"
    }

    stages {
        // שלב 1: משיכת הקוד מ-GitHub
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // שלב 2: בניית ה-Images בתיקיית השורש
        stage('Build') {
            steps {
                echo 'Building Docker Images...'
                bat 'docker-compose build --no-cache'
            }
        }

        // שלב 3: הרמת המערכת ובדיקות (Testing)
        stage('Test') {
            steps {
                echo 'Running Application Tests...'
                // הרמה נקייה של הקונטיינרים
                bat 'docker-compose up -d --force-recreate'
                
                // המתנה של 8 שניות שהשרתים (Flask ו-React) יסיימו לעלות לחלוטין
                sleep time: 8, unit: 'SECONDS'
                
                echo 'Checking if Backend and Frontend ports are alive...'
                bat 'curl -f http://localhost:5001/ || exit 1'
                bat 'curl -f http://localhost:3000/ || exit 1'
                
                echo 'All health checks passed successfully!'
            }
        }

        // שלב 4: העלאת ה-Images ל-Docker Hub
        stage('Deploy to Docker Hub') {
            steps {
                echo 'Pushing Images to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    bat "docker login -u %DOCKER_HUB_USERNAME% -p %DOCKER_HUB_PASSWORD%"
                    
                    // תיוג ה-Images עם שם המשתמש שלך
                    bat "docker tag %BACKEND_IMAGE%:latest %DOCKER_HUB_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker tag %FRONTEND_IMAGE%:latest %DOCKER_HUB_USER%/%FRONTEND_IMAGE%:latest"
                    
                    // דחיפה ל-Docker Hub
                    bat "docker push %DOCKER_HUB_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker push %DOCKER_HUB_USER%/%FRONTEND_IMAGE%:latest"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up test containers...'
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                bat 'docker-compose down'
            }
        }
    }
}