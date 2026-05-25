pipeline {
    agent any

    environment {
        // שם המשתמש שלך ב-Docker Hub
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

        // שלב 2: בניית ה-Images של המערכת
        stage('Build') {
            steps {
                echo 'Building Docker Images...'
                bat 'docker-compose build'
            }
        }

        // שלב 3: בדיקות (Testing) - הרמה, המתנה ובדיקת פורטים
        stage('Test') {
            steps {
                echo 'Running Application Tests...'
                bat 'docker-compose up -d'
                
                // המתנה של 5 שניות שהשרתים יעלו בתוך דוקר
                sleep time: 5, unit: 'SECONDS'
                
                // בדיקה שה-Backend וה-Frontend מגיבים (משתמשים ב-curl על Windows)
                bat 'curl -f http://localhost:5001/ || exit 1'
                bat 'curl -f http://localhost:3000/ || exit 1'
                
                echo 'All health checks passed successfully!'
            }
        }

        // שלב 4: העלאת ה-Images ל-Docker Hub בצורה מאובטחת
        stage('Deploy to Docker Hub') {
            steps {
                echo 'Pushing Images to Docker Hub...'
                // שימוש ב-Credentials מאובטחים של ג'נקינס
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    bat "docker login -u %DOCKER_HUB_USERNAME% -p %DOCKER_HUB_PASSWORD%"
                    
                    // תיוג ה-Images
                    bat "docker tag %BACKEND_IMAGE%:latest %DOCKER_HUB_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker tag %FRONTEND_IMAGE%:latest %DOCKER_HUB_USER%/%FRONTEND_IMAGE%:latest"
                    
                    // העלאה (Push) ל-Docker Hub
                    bat "docker push %DOCKER_HUB_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker push %DOCKER_HUB_USER%/%FRONTEND_IMAGE%:latest"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up test containers...'
            bat 'docker-compose down'
        }
    }
}