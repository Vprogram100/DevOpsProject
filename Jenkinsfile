pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Debug Directory Structure') {
            steps {
                echo '=== Printing Root Folders ==='
                bat 'dir'
                
                echo '=== Checking if DevOpsProject exists as subfolder ==='
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    dir('DevOpsProject') {
                        bat 'dir'
                    }
                }
            }
        }
    }
}