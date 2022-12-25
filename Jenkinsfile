pipeline {
    agent any
    
    stages {
        stage ('Sonar quality checks') {
            agent {
                docker {
                    image 'maven'
                }
            }
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage ('Quality gate status') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage ('docker image creation and push to nexus repo') {
            steps {
                withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_cred')]) {
                    sh '''
                        docker build -t 35.74.253.177:8083/springapp:${VERSION} .
                        docker login -u admin -p $nexus_cred 35.74.253.177:8083
                        docker push 35.74.253.177:8083/springapp:${VERSION}
                        docker rmi 35.74.253.177:8083/springapp:${VERSION}
                    '''
                }
            }
        }
    }
}
