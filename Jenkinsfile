pipeline {
    agent any

    environment {
        GCP_PROJECT_ID = 'crack-atlas-430705-a1'
        GCP_CREDENTIALS = '/var/lib/jenkins/gcp-key.json'
    }

    triggers {
        pollSCM('H/5 * * * *') // Adjust polling frequency if needed
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/VSD065/example-voting-app.git'
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.yml up --abort-on-container-exit'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh 'docker-compose build'
                }
            }
        }

        stage('Deploy to GCP') {
            steps {
                script {
                    sh '''
                        gcloud auth activate-service-account --key-file=$GCP_CREDENTIALS
                        gcloud config set project $GCP_PROJECT_ID
                        gcloud compute ssh app-instance --zone=asia-south2-c --command "docker-compose up -d"
                    '''
                }
            }
        }

        stage('Rollback') {
            when {
                expression { currentBuild.result == 'FAILURE' }
            }
            steps {
                script {
                    sh 'echo "Rollback logic here"'
                    // Implement actual rollback logic
                }
            }
        }
    }

    post {
        always {
            echo 'This will always run'
            cleanWs() // Clean workspace after the build
        }
    }
}

