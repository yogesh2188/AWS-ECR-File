pipeline {
    
  agent any
  
   environment {
      registry = "public.ecr.aws/t9h4g4g8/ecr_repo"
  }
  
   stages {
        stage('Cloning Github project/source code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/RutujaPawal/pushDockerImage_to_AWSECRrepo.git']]])
            }
        }
        
        stage ('Build') {
            steps {
                sh 'mvn clean install'           
            }
        }
        
        stage('Building image') {
            steps{
                 script {
                   dockerImage = docker.build registry
               }
            }
        }
        
        stage('Logging into AWS ECR') {
          steps {
              script {
              sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/t9h4g4g8'
              }
            }
        }
        
        stage('Pushing Image on ECR') {
          steps {
              script {
              sh 'docker push public.ecr.aws/t9h4g4g8/ecr_repo:latest'
              }
            }
        } 
        
        // Stopping Docker containers for cleaner Docker run
       stage('stop previous containers') {
          steps {
            sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
           }
        }
        
        stage('Docker Run') {
          steps{
             script {
                sh 'docker run -d -p 8097:8080 --rm --name mypythonContainer public.ecr.aws/t9h4g4g8/ecr_repo:latest'
              }
            }
        }
      
    }
    
}    
