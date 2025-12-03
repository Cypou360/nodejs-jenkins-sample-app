pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    // Quality Gate attend 1min
                    def scannerHome = tool 'SonarScanner'  // Nom outil Sonar configuré
                    withSonarQubeEnv('SonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=node-app \
                            -Dsonar.sources=. \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info"
                    }
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        
        stage('Deploy') {
            steps {
                sh """
            docker compose down
            docker compose up -d --build
            """
            }
        }
    }
    
    /* post {
        // TODO: Partie bonus
    }*/
}