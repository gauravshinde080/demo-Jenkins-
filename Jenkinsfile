pipeline{

    agent any

    stages{

        stage('Git Checkout')
        {
            steps
            {
               git branch: 'main', url: 'https://github.com/omkars8/demo-counter-app.git' 
            }
        }
    }
}
