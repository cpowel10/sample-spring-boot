pipeline {
    agent none
        environment {
        ENV_DOCKER = credentials('dockerhub')
        DOCKERIMAGE = "cpowell99/practice_lab"
        EKS_CLUSTER_NAME = "demo-cluster"
        ENV_SONARQUBE = credentials('sonarqube')
    }
    stages {
        stage('build') {
            agent {
                docker { image 'openjdk:11-jdk' }
            }
            steps {
                sh 'chmod +x gradlew && ./gradlew build jacocoTestReport'
            }
        }
        stage('sonarqube') {
        agent {
            docker { image 'sonarsource/sonar-scanner-cli:latest' } }
            steps {
                sh 'echo scanning!'
            }
        }
        stage('docker build') {
            steps {
                sh 'echo docker build'
            }
        }
        stage('docker push') {
            steps {
                sh 'echo docker push!'
                }
            }
        stage('Deploy App') {
            steps {
                sh 'echo deploy to kubernetes'               
            }
        }
    }
}
