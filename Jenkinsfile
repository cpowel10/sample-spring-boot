pipeline {
    agent none
        environment {
        ENV_DOCKER = credentials('dockerhub')
        DOCKERIMAGE = "practice_lab"
        EKS_CLUSTER_NAME = "demo-cluster"
        SONAR_TOKEN = credentials('sonarqube')
        image = ''
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
        //     agent {
        //         docker { image 'sonarsource/sonar-scanner-cli:latest' } 
        //     }
        //     steps {
        //         unstash 'build'
        //         sh 'sonar-scanner -X'
        //     }
        // }
        stage('docker build') {
            agent any
            steps {
                sh 'echo docker build'
                unstash 'build'
                script{
                    image = docker.build("$ENV_DOCKER_USR/$DOCKERIMAGE")
                }
            }
        }
        stage('docker push') {
            agent any
            steps {
                sh 'echo docker push!'

                script{
                    docker.withRegistry('', 'dockerhub'){
                        image.push("$BUILD_ID")
                        image.push('latest')
                    }
                }
            }
        }
        stage('Deploy App') {
            agent any
            steps {
                sh 'echo deploy to kubernetes'               
            }
        }
    }
}
