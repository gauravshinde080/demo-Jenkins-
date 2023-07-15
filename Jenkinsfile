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
    }
}
