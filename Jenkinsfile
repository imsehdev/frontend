
pipeline {

    agent any

    environment {
        IMAGE_NAME     = 'frontend'
        CONTAINER_NAME = 'frontend-container'
        IMAGE_TAG      = "${BUILD_NUMBER}"
        HOST_PORT      = '8081'
        CONTAINER_PORT = '80'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'

                checkout scm
            }
        }

        stage('Test') {
            steps {
                echo 'Validating application files'

                sh '''
                    test -f index.html
                    test -f Dockerfile
                    test -s index.html

                    grep -q "Welcome to DevOps" index.html

                    echo "Application validation successful"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'

                sh '''
                    docker build \
                        -t ${IMAGE_NAME}:${IMAGE_TAG} \
                        -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying frontend container'

                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --restart unless-stopped \
                        --name ${CONTAINER_NAME} \
                        -p ${HOST_PORT}:${CONTAINER_PORT} \
                        ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying frontend deployment'

                sh '''
                    sleep 3

                    docker ps --filter name=${CONTAINER_NAME}

                    docker inspect \
                        --format='Container Status: {{.State.Status}}' \
                        ${CONTAINER_NAME}

                    curl -f http://localhost:${HOST_PORT}/

                    echo "Frontend deployment verified successfully"
                '''
            }
        }
    }

    post {

        success {
            echo 'Frontend application deployed successfully!'
            echo 'Website URL: http://YOUR-EC2-PUBLIC-IP:8081'
        }

        failure {
            echo 'Frontend deployment failed!'
        }

        always {
            echo 'Pipeline execution completed'
        }
    }
}

