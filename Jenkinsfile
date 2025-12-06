pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_HUB_USERNAME = 'prem1102'
        FRONTEND_IMAGE = "${DOCKER_HUB_USERNAME}/revtickets-frontend"
        BACKEND_IMAGE = "${DOCKER_HUB_USERNAME}/revtickets-backend"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'Prem',
                    url: 'https://github.com/Saikumarsiruvati/RevTicket.git'
            }
        }
        
        stage('Build Frontend') {
            steps {
                dir('revtickets-frontend') {
                    bat 'npm install --legacy-peer-deps'
                    bat 'npm run build --prod'
                }
            }
        }
        
        stage('Build Backend') {
            steps {
                dir('revtickets-backend') {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build Docker Images') {
            steps {
                script {
                    bat "docker build -t ${FRONTEND_IMAGE}:${IMAGE_TAG} -t ${FRONTEND_IMAGE}:latest ./revtickets-frontend"
                    bat "docker build -t ${BACKEND_IMAGE}:${IMAGE_TAG} -t ${BACKEND_IMAGE}:latest ./revtickets-backend"
                }
            }
        }
        
        stage('Docker Login') {
            steps {
                script {
                    bat "echo %DOCKER_HUB_CREDENTIALS_PSW% | docker login -u %DOCKER_HUB_CREDENTIALS_USR% --password-stdin"
                }
            }
        }
        
        stage('Push Docker Images') {
            steps {
                script {
                    bat "docker push ${FRONTEND_IMAGE}:${IMAGE_TAG}"
                    bat "docker push ${FRONTEND_IMAGE}:latest"
                    bat "docker push ${BACKEND_IMAGE}:${IMAGE_TAG}"
                    bat "docker push ${BACKEND_IMAGE}:latest"
                }
            }
        }
        
        stage('Deploy (Optional)') {
            steps {
                script {
                    bat "docker-compose down || exit 0"
                    bat "docker-compose up -d"
                }
            }
        }
    }
    
    post {
        always {
            bat 'docker logout'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
