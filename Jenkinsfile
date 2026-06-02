pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t de-dashboard .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm de-dashboard pytest tests/ -v'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker stop de-dashboard 2>/dev/null || true
                    docker rm de-dashboard 2>/dev/null || true

                    docker run -d \
                    --name de-dashboard \
                    -p 5001:5001 \
                    de-dashboard
                '''
            }
        }
    }
}