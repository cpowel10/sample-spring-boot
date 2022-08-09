pipeline {
    agent any
    environment {
        ENV_DOCKER = credentials('dockerhub')
        DOCKERIMAGE = "sre-lab"
        EKS_CLUSTER_NAME = "demo-cluster"
        SONAR_TOKEN = credentials('sonar-token')
        image = ''
    }
    stages {
        stage('build') {
            steps {
                container('jdk') {
                    sh 'gradle build jacocoTestReport'
                    stash includes: 'build/**/*', name: 'build'
                }
            }
        }
        stage('sonarqube') {
        agent {
            docker { image 'sonarsource/sonar-scanner-cli' } }
            steps {
                container('sonar') {
                    unstash 'build'
                    sh 'sonar-scanner'
                }
            }
        }
        stage('docker build') {
            steps {
                container('docker') {
                    echo 'Starting to build docker image'
                    unstash 'build'

                    script {
                        image = docker.build("$ENV_DOCKER_USR/$DOCKERIMAGE:latest")
                    }
                }
            }
        }
        stage('docker push') {
            steps {
                container('docker') {
                    echo 'Pushing image to registry'

                    script {
                        docker.withRegistry('', 'dockerhub') {
                            image.push()
                            image.push("$BUILD_ID")
                        }
                    }
                }
            }
        }
        stage('Deploy App') {
            steps {
                container('kubectl') {
                    echo 'Deploying to kubernetes'

                    withAWS(credentials:'aws-credentials') {
                        sh 'aws eks update-kubeconfig --name demo-cluster'
                        sh 'chmod +x deployment-status.sh && ./deployment-status.sh'
                        sh "kubectl set image deployment sample-spring-boot -n chris-powell springboot-sample=$ENV_DOCKER_USR/$DOCKERIMAGE:$BUILD_ID"
                    }
                }     
            }
        }
    }
}
