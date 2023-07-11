pipeline {

    tools {
        maven 'maven3'
    }
    agent any

    environment {
        registry = "191327398744.dkr.ecr.us-west-2.amazonaws.com/my-docker"
    }
    stages {
        stage('Checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Nehalnarnaware/docker-spring-boot-EKS.git']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 191327398744.dkr.ecr.us-west-2.amazonaws.com"
                    sh "docker push 191327398744.dkr.ecr.us-west-2.amazonaws.com/my-docker:latest"
                    
                }
            }
        }
        
        stage ("Helm deploy") {
            steps {
                script{
                     sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                 }
                }
            }
                

    }
}
