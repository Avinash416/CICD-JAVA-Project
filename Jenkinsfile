pipeline{
    agent any
    stages{
        stage("sonar Quality check"){
            steps{
               script{
                withSonarQubeEnv(credentialsId: 'jenkins') {
                    sh 'chmod +x gradlew'
                    sh './gradlew sonar --warning-mode all'
                }
               }
            }
           
            
        }
    }
   
}