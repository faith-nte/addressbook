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
                git branch: 'jenkins', 
                url: 'https://github.com/faith-nte/addressbook.git'
            }
        }
/*
        stage('Install Dependencies') {
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    sh 'cd frontend && npm install --no-fund --no-audit --legacy-peer-deps || true'
                    sh 'cd backend && npm install --no-fund --no-audit --legacy-peer-deps || true'
                    sh 'cd tests && npm install --no-fund --no-audit --legacy-peer-deps || true'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo "Skipping tests for now - will implement proper testing later"
                }
            }
        }

        stage('Build Frontend (React + Vite)') {
            steps {
                script {
                    sh '''
                    cd frontend
                    rm -rf node_modules
                    npm cache clean --force
                    npm install vite --no-fund --no-audit
                    npm list vite
                    NODE_ENV=production npm run build || npm run build -- --debug
                    '''
                }
            }
        }
        */

        stage('Build and Push Docker Images') {
            steps {
                script {
                    sh 'docker build -t my-frontend -f Dockerfile.jenkins .'
                    sh 'docker tag my-frontend my-dockerhub-user/my-frontend:latest'
                    sh 'echo $DOCKER_HUB_CREDENTIALS_PSW | docker login -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin'
                    sh 'docker push my-dockerhub-user/my-frontend:latest'
                    sh 'docker build -t my-backend ./backend'
                    sh 'docker tag my-backend my-dockerhub-user/my-backend:latest'
                    sh 'docker push my-dockerhub-user/my-backend:latest'
                }
            }
        }

        /*
        stage('Deploy with Terraform') {
            steps {
                script {
                    sh '''
                    cd infra
                    export AWS_ACCESS_KEY_ID=$AWS_CREDENTIALS_USR
                    export AWS_SECRET_ACCESS_KEY=$AWS_CREDENTIALS_PSW
                    terraform init
                    terraform apply -auto-approve
                    '''
                }
            }
        }
        */
    }
}