pipeline{

    agent any
    tools {
  jdk 'jdk17'
  maven 'maven3'
  dockerTool 'docker'
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
                    nexusUrl: 'localhost:8081', 
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
                    bat 'docker image build -t demoapp .'
                    bat 'docker image tag demoapp omkar008/demoapp'
                    bat 'docker image tag demoapp omkar008/demoapp:latest'
                }
            }
        }

           stage('push image to the dockerHUb'){

            steps{

                script{
                        
                        bat 'docker login -u omkar008 -p dckr_pat_IEi85vDRa0w8AHT2YgogVQDe9_M'
                        bat 'docker image push omkar008/demoapp'
                        bat 'docker image push omkar008/demoapp:latest'
                    
                }
            }
        }
    }
}
