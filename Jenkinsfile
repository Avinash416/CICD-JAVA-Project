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

         stage('indentifying misconfigs using datree in helm charts'){
            steps{
                script{

                    dir('kubernetes/') {
                        withEnv(['DATREE_TOKEN=GJdx2cP2TCDyUY3EhQKgTc']) {
                              sh 'helm datree test myapp/'
                        }
                    }
                }
            }
        }

         stage("pushing the helm charts to nexus"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_password', variable: 'docker_pass')]) {
                          dir('kubernetes/') {
                             sh '''
                                 helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                                 tar -czvf  myapp-${helmversion}.tgz myapp/
                                 curl -u admin:$docker_password http://34.125.214.226:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                            '''
                          }
                    }
                }
            }
        }
    }
    // post {
	// 	always {
	// 		mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "boseashu13@gmail.com";  
	// 	 }
	//    }

}