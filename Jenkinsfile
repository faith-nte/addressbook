pipeline {
    agent any
    tools {
        nodejs 'NodeJS' // This must match a NodeJS installation name in Jenkins
    }
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        AWS_CREDENTIALS = credentials('aws-credentials')
        DB_PASSWORD = credentials('db-password')
        MAILGUN_API_KEY = credentials('mailgun-api-key')
        MAILGUN_DOMAIN = credentials('mailgun-domain')
        MAILGUN_RECIPIENT = 'faithskool4u@gmail.com'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'jenkins', url: 'https://github.com/faith-nte/addressbook.git'
            }
        }
    }
}

pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    environment {
        MAILGUN_RECIPIENT = 'faithskool4u@gmail.com'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies for frontend, backend, and tests...'
                sh 'cd frontend && npm ci --no-fund --no-audit'
                sh 'cd backend && npm ci --no-fund --no-audit'
                sh 'cd tests && npm ci --no-fund --no-audit'
            }
        }

        // Optional: Uncomment when ready to run tests
        // stage('Run Tests') {
        //     steps {
        //         echo 'Running tests...'
        //         sh 'cd tests && npm test'
        //     }
        //     post {
        //         failure {
        //             echo 'Tests failed but continuing the pipeline.'
        //         }
        //     }
        // }

        // Optional: Uncomment when ready to build frontend
        // stage('Build Frontend (React + Vite)') {
        //     steps {
        //         echo 'Building frontend...'
        //         sh 'cd frontend && npm run build'
        //     }
        // }

        // Optional: Add more stages like Docker build, Terraform deploy, etc.
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
