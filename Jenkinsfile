pipeline{
    agent any
    environment{
        VERSION="${env.BUILD_ID}"
    }
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
        stage("docker build and docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_password', variable: 'docker_pass')]) {
                         sh '''
                                docker build -t 13.235.81.182:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_pass 13.235.81.182:8083 
                                docker push  13.235.81.182:8083/springapp:${VERSION}
                                docker rmi 13.235.81.182:8083/springapp:${VERSION}
                         '''
                     }
                }
            }
        }
    }
   
}