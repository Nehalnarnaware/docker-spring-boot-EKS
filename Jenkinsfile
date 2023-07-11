pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "191327398744.dkr.ecr.us-west-2.amazonaws.com/my-docker"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                 checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Nehalnarnaware/docker-spring-boot-EKS.git']])
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
          dockerImage.tag("$BUILD_NUMBER")
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Login to ECR'){
        steps{
            script{
           sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 191327398744.dkr.ecr.us-west-2.amazonaws.com"
            }
        }
    }
   
    stage('Pushing to ECR') {
     steps{
         script {
                sh 'docker build -t my-docker .'
                sh 'docker tag my-docker:latest 191327398744.dkr.ecr.us-west-2.amazonaws.com/my-docker:latest'
                sh 'docker push 191327398744.dkr.ecr.us-west-2.amazonaws.com/my-docker:$BUILD_NUMBER'
            }
        
        }
    }
    
        stage ('Helm Deploy') {
          steps {
            script {
              
                sh "helm upgrade first --install mychart --namespace helm-deployment-$BUILD_NUMBER --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}
