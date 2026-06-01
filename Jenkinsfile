pipeline {
    agent any

    stages {
        stage('Install') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                sh 'pytest'
            }
        }

        stage('Build Docker') {
            steps {
                sh 'docker build -t de-dashboard .'
            }
        }

        stage('Run') {
            steps {
                sh 'docker run -d -p 5001:5001 de-dashboard'
            }
        }
    }
}
