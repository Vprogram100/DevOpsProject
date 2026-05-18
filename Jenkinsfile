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
                // המעבר לתיקייה DevOpsProject מבטיח שדוקר יראה את הקובץ
                dir('DevOpsProject') {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        bat 'docker compose down'
                    }
                }
            }
        }

        stage('Docker Build & Up') {
            steps {
                dir('DevOpsProject') {
                    bat 'docker compose up -d --build'
                }
            }
        }

        stage('Sanity Check') {
            steps {
                dir('DevOpsProject') {
                    bat 'docker ps'
                }
            }
        }
    }
}