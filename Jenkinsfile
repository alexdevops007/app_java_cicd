pipeline{

    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }

    stages{
        stage('Sonarqube quality check'){

            agent{
                docker{
                    image 'maven'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {

                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }

        stage('docker build and push to Nexus repo'){

            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_creds')]) {
                        sh '''
                         docker build -t 54.82.247.163:8083/springapp:${VERSION} .
                         docker login -u admin -p $nexus_creds 54.82.247.163:8083
                         docker push 54.82.247.163:8083/springapp:${VERSION}
                         docker rmi 54.82.247.163:8083/springapp:${VERSION}
                        '''
                    }
                }
            }
        }
    }
}