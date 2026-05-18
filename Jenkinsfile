pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Debug - List Files') {
            steps {
                echo "--- Checking Root Folder ---"
                bat 'dir'
                
                echo "--- Checking DevOpsProject Folder ---"
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    dir('DevOpsProject') {
                        bat 'dir'
                    }
                }
            }
        }
    }
}