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
                    try {
                        withSonarQubeEnv('SonarQube') {
                            sh '''
                                sonar-scanner \
                                  -Dsonar.projectKey=nodejs-jenkins-sample-app \
                                  -Dsonar.projectName='Node.js Jenkins Sample App' \
                                  -Dsonar.sources=. \
                                  -Dsonar.exclusions=node_modules/**,test.js \
                                  -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                            '''
                        }
                        timeout(time: 3, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: false
                        }
                    } catch (Exception e) {
                        echo "SonarQube analysis failed: ${e.message}"
                        echo "Continuing pipeline without Quality Gate check"
                    }
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