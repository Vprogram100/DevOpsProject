pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Down') {
            steps {
                // השימוש ב-catchError מונע מהבילד לקרוס אם אין עדיין קונטיינרים ישנים להוריד
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'docker compose -f docker-compose.yml down'
                }
            }
        }

        stage('Docker Build & Up') {
            steps {
                bat 'docker compose -f docker-compose.yml up -d --build'
            }
        }

        stage('Sanity Check') {
            steps {
                bat 'docker ps'
            }
        }
    }
}