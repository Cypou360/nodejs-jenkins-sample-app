pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            sh 'git pull'
        }
        
        stage('Install Dependencies') {
            sh 'npm install'
        }
        
        stage('Run Tests') {
            sh 'npm test'
        }
        
        stage('Build Docker Image') {
            sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
        }
        
        stage('Deploy') {
            sh """
                docker stop ${DOCKER_IMAGE} || true
                docker rm ${DOCKER_IMAGE} || true
                docker run -d --name ${DOCKER_IMAGE} -p 80:3000 ${DOCKER_IMAGE}:${DOCKER_TAG}
            """
        }
    }
    
    post {
        // TODO: Partie bonus
    }
}