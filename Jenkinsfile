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

        // שלב 2: בניית האימג'ים של דוקר
        stage('Build') {
            steps {
                bat 'docker-compose build'
            }
        }

        // שלב 3: הרמת הקונטיינרים ובדיקה בסיסית
        stage('Test') {
            steps {
                // מוריד קונטיינרים ישנים אם רצים כדי למנוע התנגשות שמות
                bat 'docker-compose down' 
                
                // מרים את האפליקציה ברקע
                bat 'docker-compose up -d' 
                
                // מחכה 5 שניות שהכל יעלה ומודא שהפקודה עברה
                sleep time: 5, unit: 'SECONDS'
            }
        }

        // שלב 4: דחיפה ל-Docker Hub
        stage('Deploy to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    // התחברות לחשבון שלך
                    bat "docker login -u %DOCKER_HUB_USERNAME% -p %DOCKER_HUB_PASSWORD%"
                    
                    // תיוג (Tag)
                    bat "docker tag %BACKEND_IMAGE%:latest %DOCKER_HUB_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker tag %FRONTEND_IMAGE%:latest %DOCKER_HUB_USER%/%FRONTEND_IMAGE%:latest"
                    
                    // דחיפה (Push)
                    bat "docker push %DOCKER_HUB_USER%/%BACKEND_IMAGE%:latest"
                    bat "docker push %DOCKER_HUB_USER%/%FRONTEND_IMAGE%:latest"
                }
            }
        }
    }

    post {
        always {
            // בסיום (לא משנה אם הצליח או נכשל) - מכבים את הקונטיינרים של הבדיקה שלא יתפסו זיכרון
            bat 'docker-compose down'
        }
    }
}


