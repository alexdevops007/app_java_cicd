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
                         docker build -t 44.212.40.205:8083/springapp:${VERSION} .
                         docker login -u admin -p $nexus_creds 44.212.40.205:8083
                         docker push 44.212.40.205:8083/springapp:${VERSION}
                         docker rmi 44.212.40.205:8083/springapp:${VERSION}
                        '''
                    }
                }
            }
        }
        /*
        stage('Identifying misconfigs using datree in helm charts'){

            steps{
                script{
                    dir('kubernetes/myapp') {
                        withEnv(['DATREE_TOKEN=8dfdac87-134e-4126-8206-a05413cf4ff0']) {
                            sh 'helm datree test .'
                        }
                    }
                }
            }

        }
        */
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "alexsms.devops@gmail.com";  
		}
	}
}