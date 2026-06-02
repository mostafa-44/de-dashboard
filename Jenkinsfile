pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/mostafa-44/de-dashboard.git', branch: 'main'
            }
        }

        stage('List Files') {
            steps {
                sh 'ls -la'
            }
        }

        stage('Show Python Version') {
            steps {
                sh 'python3 --version || echo "Python not available in Jenkins"'
            }
        }

        stage('Run Safe Check') {
            steps {
                sh 'echo "Pipeline running successfully without Docker"'
            }
        }
    }
}
