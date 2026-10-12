pipeline {
    agent any

    tools {
        maven 'Maven3'      // names set in Manage Jenkins > Tools
        jdk   'JDK17'
    }

    environment {
        // lets Jenkins find docker on macOS
        PATH+DOCKER = "/usr/local/bin:/opt/homebrew/bin"
        IMAGE_NAME = 'jenkins-docker-demo'
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/KamalaSwaminathan/jenkins-docker-demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Docker Run') {
            steps {
                sh "docker run --rm --name demo-${BUILD_NUMBER} ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }

    post {
        success { echo 'Pipeline completed successfully!' }
        failure { echo 'Pipeline failed. Check the console output.' }
    }
}
