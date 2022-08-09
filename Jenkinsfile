pipeline {
    agent any
        environment {
        ENV_DOCKER = credentials('dockerhub')
        DOCKERIMAGE = "sre-lab"
        EKS_CLUSTER_NAME = "demo-cluster"
        image = ''
        ENV_SONARQUBE = credentials('sonar-token')
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
        //     docker { image 'sonarsource/sonar-scanner-cli' } }
        //     steps {
        //         sh 'echo scanning!'
        //         unstash 'build'
        //         sh 'sonar-scanner -X'
        //     }
        // }
        stage('docker build') {
            steps {
                unstash 'build'
                sh 'echo docker build'
                script{
                    image = docker.build("$ENV_DOCKER_USR/$DOCKERIMAGE")
                }
                
            }
        }
        stage('docker push') {
            steps {
                sh 'echo docker push!'
                script{
                    docker.withRegistry('','dockerhub'){
                        image.push("$BUILD_ID")
                        image.push('latest')
                    }
                }
            }
        }
        // stage('Deploy App') {
        //     agent {
        //         docker {
        //             image 'jshimko/kube-tools-aws:3.8.1'
        //             args '-u root --privileged'
        //         }
        //     }
        //     steps {
        //         sh 'echo deploy to kubernetes' 

        //         withAWS(credentials:'aws-credentials'){
        //             sh 'aws eks update-kubeconfig --name demo-cluster'
        //             sh 'chmod +x deployment-status.sh && ./deployment-status.sh'
        //             sh "kubectl set image deployment sample-spring-boot -n chris-powell springboot-sample=$ENV_DOCKER_USR/$DOCKERIMAGE:$BUILD_ID"
        //         }              
        //     }
        // }
    }
}
