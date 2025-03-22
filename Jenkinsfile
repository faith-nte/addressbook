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
                git branch: 'jenkinsbranch', url: 'https://github.com/faith-nte/addressbook.git'
            }
        }
    }
}