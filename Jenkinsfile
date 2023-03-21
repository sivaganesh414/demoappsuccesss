pipeline {
    agent any
    tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "maven3"
   }


    stages {
        stage('Gitcheckout') {
            steps {
                git branch: 'main', url: 'https://github.com/sivaganesh414/demo-counter-app.git'
            }
        }
          stage('testing') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Integration Testing') {
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Static Code Analysis'){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sona-api') {
                   sh 'mvn clean package sonar:sonar'
                 }
                    
                }
            }
        }
        stage('Nexus'){
            steps{
                script{
                   
            nexusArtifactUploader artifacts: [
                [
                    artifactId: 'springboot',
             classifier: '', 
            file: 'target/Uber.jar',
            type: 'jar']
            ], 
            credentialsId: 'nexuscred', 
            groupId: 'com.example', 
            nexusUrl: 'ec2-18-222-166-36.us-east-2.compute.amazonaws.com:8081/', 
            nexusVersion: 'nexus3', 
            protocol: 'http',
             repository: 'demo-releases',
             version: '1.0.0'
                 }
                    
                }
            }
        stage('image') {
            steps {
                script{
                  sh 'docker build -t $JOB_NAME:v1.$BUILD_ID .'
                  sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sivaganesh414/$JOB_NAME:v1.$BUILD_ID'
                   sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sivaganesh414/$JOB_NAME:latest'
                }
            }

        }
        stage('Push to  Docker image') {
            steps {
                script{
                    withCredentials([string(credentialsId: 'docker_hub_cred', variable: 'docker_hub_cred')]) {
   
                  sh 'docker login -u sivaganesh414 -p ${docker_hub_cred}'
                  sh 'docker image push sivaganesh414/$JOB_NAME:v1.$BUILD_ID'
                  sh 'docker image push sivaganesh414/$JOB_NAME:latest'
                }
                }
            }
        }
    }
}
