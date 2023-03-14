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
                timeout(time:1, unit:'HOURS'){
                    def qg= waitForQualityGate()
                    if(qg.status != 'OK'){
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }

                }
               }
            }
           
            
        }
    }
   
}