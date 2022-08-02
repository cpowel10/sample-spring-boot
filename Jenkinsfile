pipeline {
    agent none
        environment {
            ENV_DOCKER = credentials('dockerhub')
            DOCKERIMAGE = "dummy/dummy"
            EKS_CLUSTER_NAME = "demo-cluster"
            SONAR_TOKEN = credentials('sonar-token')
        }
    stages {
        stage('build & submit report') {
            agent {
                docker { image 'openjdk:11-jdk' }
            }
            steps {
                sh 'chmod +x gradlew && ./gradlew build jacocoTestReport'
            }
            steps {
                sh 'chmod +x gradlew && ./gradlew sonarqube'
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