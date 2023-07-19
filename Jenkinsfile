pipeline{

    agent any
    tools {
  jdk 'jdk17'
  maven 'maven3'
    }

    stages{

        stage('Git Checkout')
        {
            steps
            {
               git branch: 'main', url: 'https://github.com/omkars8/demo-counter-app.git' 
            }
        }
        stage('UNIT Testing')
        {
            steps
            {
              bat 'mvn test'
            }
        }
        stage('INTEGRATION Testing')
        {
            steps
            {
              bat 'mvn verify -DskipUnitTests'
            }
        }
        stage('Maven Bulid')
        {
            steps
            {
              bat 'mvn clean install'
            }
        }
          stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar1') {
                        
                        bat 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }

        stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar1'
                    }
                }
            }
        stage('upload war file to nexus'){

            steps{

                script{

                    def readPomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"

                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', file: 'target/Uber.jar', 
                            type: 'jar'
                            ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '3.226.244.16:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"
                }
            }
        }

     stage('Docker Image Build'){

            steps{

                script {
                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID vikashashoke/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID vikashashoke/$JOB_NAME:latest'
                }
            }
        }

        stage('push image to the dockerHUb'){

            steps{

                script{

                    withCredentials([string(credentialsId: 'dockerhub_pwd', variable: 'dockerhub_cred')]) {
                        
                        sh 'docker login -u vikashashoke -p ${dockerhub_cred}'
                        sh 'docker image push vikashashoke/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image push vikashashoke/$JOB_NAME:latest'
                    }
                }
            }
        }
    }
}
