pipeline {
    agent any

    environment {
        // Define environment variables here
        GCP_PROJECT_ID = 'crack-atlas-430705-a1'
        GCP_CREDENTIALS = '/var/lib/jenkins/gcp-key.json'
    }
   triggers {
        // Poll SCM every minute
        pollSCM('H/1 * * * *')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/VSD065/example-voting-app.git'
            }
        }

        stage('Run Tests') {
            steps {
                // Run tests here, for example, using Docker Compose
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
                    // Use gcloud CLI to deploy the application
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
                    // Rollback steps, such as redeploying the previous version or using a different strategy
                    sh 'echo "Rollback logic here"'
                }
            }
        }
    }

    post {
        always {
            // Cleanup or notification steps, if any
        }
    }
}
