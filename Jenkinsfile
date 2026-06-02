
pipeline {
    agent any
 
    environment {
        IMAGE_NAME      = "mostafa-44/de-dashboard"
        IMAGE_TAG       = "${BUILD_NUMBER}"
        CONTAINER_NAME  = "de-dashboard"
        APP_PORT        = "5001"
        DOCKERHUB_CREDS = credentials('dockerhub-credentials')
    }
 
    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                echo "=== Checking out source code ==="
                checkout scm
            }
        }
 
        stage('Install Dependencies') {
            steps {
                echo "=== Installing Python dependencies ==="
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }
 
        stage('Run Tests') {
            steps {
                echo "=== Running unit tests with pytest ==="
                sh '''
                    . venv/bin/activate
                    pytest tests/ -v --tb=short --junitxml=test-results.xml
                '''
            }
            post {
                always {
                    junit 'test-results.xml'
                }
            }
        }
 
        stage('Build Docker Image') {
            steps {
                echo "=== Building Docker image: ${IMAGE_NAME}:${IMAGE_TAG} ==="
                sh '''
                    docker build \
                        --no-cache \
                        -t ${IMAGE_NAME}:${IMAGE_TAG} \
                        -t ${IMAGE_NAME}:latest \
                        .
                '''
            }
        }
 
        stage('Push to Docker Hub') {
            steps {
                echo "=== Pushing image to Docker Hub ==="
                sh '''
                    echo "${DOCKERHUB_CREDS_PSW}" | docker login -u "${DOCKERHUB_CREDS_USR}" --password-stdin
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:latest
                '''
            }
        }
 
        stage('Deploy Container') {
            steps {
                echo "=== Deploying container on port ${APP_PORT} ==="
                sh '''
                    # Stop and remove old container if running
                    docker stop ${CONTAINER_NAME} 2>/dev/null || true
                    docker rm   ${CONTAINER_NAME} 2>/dev/null || true
 
                    # Run new container
                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        --restart unless-stopped \
                        -p ${APP_PORT}:5001 \
                        ${IMAGE_NAME}:${IMAGE_TAG}
 
                    echo "✅ Container deployed successfully"
                    docker ps | grep ${CONTAINER_NAME}
                '''
            }
        }
 
        stage('Health Check') {
            steps {
                echo "=== Verifying application is running ==="
                sh '''
                    sleep 5
                    curl -f http://localhost:${APP_PORT}/ || (echo "❌ Health check failed" && exit 1)
                    echo "✅ Health check passed — app is live at http://localhost:${APP_PORT}"
                '''
            }
        }
    }
 
    post {
        success {
            echo "🎉 Pipeline SUCCESS — Build #${BUILD_NUMBER} deployed as ${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo "❌ Pipeline FAILED — Check the logs above for details"
            // Optional: add email/Slack notification here
            // mail to: 'you@example.com', subject: "Build ${BUILD_NUMBER} failed", body: "Check Jenkins."
        }
        always {
            sh 'docker logout || true'
            cleanWs()
        }
    }
}