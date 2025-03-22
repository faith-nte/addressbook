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
            agent {
                docker {
                    image 'node:16'
                    reuseNode true
                }
            }
            steps {
                sh 'cd frontend && npm ci'
                sh 'cd backend && npm ci'
                sh 'cd tests && npm ci'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'cd tests && npm test'
            }
        }

        stage('Build and Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    docker build -t my-frontend ./frontend
                    docker tag my-frontend my-dockerhub-user/my-frontend:latest
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push my-dockerhub-user/my-frontend:latest
                    '''
                }
            }
        }

        stage('Deploy with Terraform') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'aws-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    cd infra
                    terraform init
                    terraform apply -auto-approve
                    '''
                }
            }
        }
    }

    post {
        success {
            withCredentials([string(credentialsId: 'mailgun-api-key', variable: 'MG_API_KEY'),
                             string(credentialsId: 'mailgun-domain', variable: 'MG_DOMAIN')]) {
                sh '''
                FROM_EMAIL="jenkins@${MG_DOMAIN}"
                curl -s --user "api:${MG_API_KEY}" \
                https://api.mailgun.net/v3/${MG_DOMAIN}/messages \
                -F from="$FROM_EMAIL" \
                -F to="${MAILGUN_RECIPIENT}" \
                -F subject="Jenkins Build Notification" \
                -F text="Your Jenkins job has completed successfully."
                '''
            }
        }
    }
}
