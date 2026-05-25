pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'varda100' 
        BACKEND_IMAGE   = "pipelinedevopsproject-backend"
        FRONTEND_IMAGE  = "pipelinedevopsproject-frontend"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'docker-compose build'
            }
        }

        stage('Test') {
            steps {
                echo 'Force cleaning any conflicting containers...'
                // פקודות הניקוי האולטימטיביות - מוחקות לפי שם ישירות מהדמון של דוקר
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'docker rm -f backend-container frontend-container'
                    bat 'docker-compose down'
                }
                
                echo 'Running Application Tests...'
                bat 'docker-compose up -d' 
                
                sleep time: 5, unit: 'SECONDS'
            }
        }

        stage('Deploy to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    bat "docker login -u %DOCKER_HUB_USERNAME% -p %DOCKER_HUB_PASSWORD%"
                    
                    bat "docker tag %BACKEND_IMAGE%:latest %DOCKER_HUB_USER%/devopsproject-backend:latest"
                    bat "docker tag %FRONTEND_IMAGE%:latest %DOCKER_HUB_USER%/devopsproject-frontend:latest"
                    
                    bat "docker push %DOCKER_HUB_USER%/devopsproject-backend:latest"
                    bat "docker push %DOCKER_HUB_USER%/devopsproject-frontend:latest"
                }
            }
        }
    }

    post {
        always {
            bat 'docker-compose down'
        }
    }
}