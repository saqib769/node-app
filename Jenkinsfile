pipeline {
    agent any

    environment {
        APP_NAME = "node-app"
        DOCKER_IMAGE = "saqib24/${APP_NAME}:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/saqib769/node-app.git'
            }
        }

        stage('Install & Build Node.js App') {
            steps {
                // Ensure Node and NPM are available
                sh 'node -v'
                sh 'npm -v'

                // Install dependencies
                sh 'npm install'

                // Run build
                sh 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${DOCKER_IMAGE} .
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-cred', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Docker image pushed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
