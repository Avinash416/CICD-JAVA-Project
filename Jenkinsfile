pipeline{
    agent any
    stages{
        stage("sonar Quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
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