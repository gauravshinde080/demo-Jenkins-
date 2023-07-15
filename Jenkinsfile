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
    }
}
