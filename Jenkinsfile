def frontendImage="ppaciekq/Frontend"
def backendImage="ppaciekq/Backend"
def dockerRegistry=""
def registryCredentials="dockerhub"

pipeline {
 
   agent {
        label 'agent'
    }

 
    parameters {
        string(name: 'backendDockerTag', defaultValue: '', description: 'Backend docker image tag')
        string(name: 'frontendDockerTag', defaultValue: '', description: 'Frontend docker image tag')
    }


    stages {


        stage('Get Code') {
            steps {
                checkout scm // Get some code from a GitHub repository
            }
        }


        stage('Adjust version') {
            steps {
                script{
                    backendDockerTag = params.backendDockerTag.isEmpty() ? "latest" : params.backendDockerTag
                    frontendDockerTag = params.frontendDockerTag.isEmpty() ? "latest" : params.frontendDockerTag
                    
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
                }
            }
        }



        stage('Remove Containers') {
            steps {
                script {
                    sh 'docker rm -f backend || true' 
                    sh 'docker rm -f frontend || true'
                }
            }
        }


        stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                       docker.withRegistry("$dockerRegistry", "$registryCredentials") {
                            sh "docker-compose up -d"
                        }
                    }
                }
            }
        }



    }
}



    post {
        always {
            script {
                sh 'docker-compose down || true'
                cleanWS()
            }
        }
    }
