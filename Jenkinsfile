pipeline {
    agent any
        environment {
        ENV_DOCKER = credentials('dockerhub')
        DOCKERIMAGE = "sre-lab"
        EKS_CLUSTER_NAME = "demo-cluster"
        image = ''
        ENV_SONARQUBE = "sonar-token"
    }
    stages {
        stage('build') {
            agent {
                docker { image 'openjdk:11-jdk' }
            }
            steps {
                sh 'chmod +x gradlew && ./gradlew build jacocoTestReport'
                stash includes: 'build/**/*' name: 'build'
            }
        }
        stage('sonarqube') {
        agent {
            docker { image 'sonarsource/sonar-scanner-cli' } }
            steps {
                sh 'echo scanning!'
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
                unstash 'build'
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
