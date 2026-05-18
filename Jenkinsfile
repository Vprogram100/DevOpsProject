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
                bat 'docker compose down'
            }
        }

        stage('Docker Build & Up') {
            steps {
                bat 'docker compose up -d --build'
            }
        }

        stage('Sanity Check') {
            steps {
                bat 'docker ps'
            }
        }
    }
}