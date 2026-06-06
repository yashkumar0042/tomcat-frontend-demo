pipeline {
    agent any

    environment {
        APP_NAME = 'tomcat-frontend-demo'
        IMAGE_NAME = 'tomcat-frontend-demo'
        CONTAINER_NAME = 'tomcat-frontend-container'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out latest code from GitHub...'
                checkout scm
            }
        }

        stage('Verify Files') {
            steps {
                echo 'Verifying project structure...'
                sh 'ls -la'
                sh 'ls -la src/main/webapp'
            }
        }

        stage('Build WAR') {
            steps {
                echo 'Building WAR file using Maven...'
                sh 'mvn clean package'
            }
        }

        stage('Verify WAR') {
            steps {
                echo 'Checking generated WAR file...'
                sh 'ls -la target'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('Stop Old Container') {
            steps {
                echo 'Stopping old container if running...'
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Run New Container') {
            steps {
                echo 'Starting new Tomcat container...'
                sh '''
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p 8081:8080 \
                    ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying deployed application...'
                sh 'sleep 10'
                sh 'curl -I http://localhost:8081'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully. Application is deployed on Tomcat Docker container.'
        }

        failure {
            echo 'Pipeline failed. Please check console logs.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
