pipeline {
    agent any
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
                stash includes: 'build/**/*', name: 'build'
            }
        }
        stage('sonarqube') {
            agent {
                docker { image 'sonarsource/sonar-scanner-cli:latest' } 
            }
            steps {
                unstash 'build'
                sh 'sonar-scanner -X'
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
