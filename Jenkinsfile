pipeline{

    agent any

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
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
    }
}