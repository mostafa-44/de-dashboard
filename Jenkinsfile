pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest tests/ -v
                '''
            }
        }

        stage('Build Docker') {
            steps {
                sh 'docker build -t de-dashboard .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker stop de-dashboard 2>/dev/null || true
                    docker rm de-dashboard 2>/dev/null || true
                    docker run -d --name de-dashboard -p 5001:5001 de-dashboard
                '''
            }
        }
    }
}