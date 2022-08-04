pipeline {
    agent none
        environment {
        ENV_DOCKER = credentials('dockerhub')
        DOCKERIMAGE = "sample-spring-boot"
        EKS_CLUSTER_NAME = "demo-cluster"
        SONAR_TOKEN = credentials('sonarqube')
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
        // stage('sonarqube') {
        // agent {
        //     docker { image 'sonarsource/sonar-scanner-cli:latest' } }
        //     steps {
        //         unstash 'build'
        //         sh 'sonar-scanner'
        //     }
        // }
        stage('docker build') {
            agent any
            steps {
                sh 'echo docker build'
                unstash 'build'
                script{
                    def image = docker.build("$ENV_DOCKER_USR/$DOCKERIMAGE")
                    docker.withRegistry('', 'dockerhub'){
                        image.push('$BUILD_ID')
                        image.push('latest')
                    }
                }
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
