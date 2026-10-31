pipeline {

    agent any

    environment {
        IMAGE_NAME = 'frontend'
        CONTAINER_NAME = 'frontend-container'
        IMAGE_TAG = 'latest'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out GitHub repository'

                checkout scm
            }
        }

        stage('Test') {
            steps {
                echo 'Validating application files'

                sh '''
                    test -f index.html
                    test -f Dockerfile

                    grep -q "Welcome to DevOps" index.html

                    echo "Validation successful"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker Image'

                sh '''
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Docker Container'

                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p 8080:80 \
                        ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    docker ps

                    echo "Frontend application deployed on port 8080"
                '''
            }
        }
    }

    post {
        success {
            echo 'Frontend deployed successfully!'
        }

        failure {
            echo 'Frontend deployment failed!'
        }

        always {
            echo 'Pipeline execution completed!'
        }
    }
}
