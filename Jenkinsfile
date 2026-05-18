
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
                sh 'docker compose down'
            }
        }

        stage('Docker Build & Up') {
            steps {
                sh 'docker compose up -d --build'
            }
        }

        stage('Sanity Check') {
            steps {
                sh 'docker ps'
            }
        }
    }
}