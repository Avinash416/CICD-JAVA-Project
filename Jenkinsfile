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
                                docker build -t 3.110.41.46:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_pass 3.110.41.46:8083 
                                docker push  3.110.41.46:8083/springapp:${VERSION}
                                docker rmi 3.110.41.46:8083/springapp:${VERSION}
                         '''
                     }
                }
            }
        }
    }
   
}