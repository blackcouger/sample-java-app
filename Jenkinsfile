pipeline {
    agent any
    tools {
    maven 'maven-391' // Use whatever name you configured
}
    environment {
        DOCKER_HUB = credentials('docker-id')
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/blackcouger/sample-java-app.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'  // or npm install, gradle build, etc.
            }
        }
        
        stage('Unit Tests') {
            steps {
                sh 'mvn test'  // or npm test, pytest, etc.
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'  // path to test results
                }
            }
        }
        
        stage('Static Code Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("docker4241/web-app:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-id') {
                        dockerImage.push()
                    }
                }
            }
        }
      
        
        stage('Integration Tests') {
            steps {
                sh 'npm run integration-test'  // or your integration test command
            }
        }
        
        stage('Approval') {
            steps {
                timeout(time: 1, unit: 'DAYS') {
                    input message: 'Deploy to production?', ok: 'Deploy'
                }
            }
        }
        
    }
    
}
