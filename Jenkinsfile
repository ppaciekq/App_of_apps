def frontendImage="ppaciekq/frontend"
def backendImage="ppaciekq/backend"
def dockerRegistry=""
def registryCredentials="dockerhub"

pipeline {
 
   agent {
        label 'agent'
    }

    tools {
        terraform 'Terraform'
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


        stage('Selenium tests') {
            steps {
                script {
                    withEnv(["PIP_BREAK_SYSTEM_PACKAGES=1"]) {
                        sh 'pip3 install -r test/selenium/requirements.txt'
                        sh 'python3 -m pytest test/selenium/selenium_test.py'
                    }
                }
            }
        }







    }


    post {
        always {
            script {
                sh 'docker-compose down || true'
                cleanWs()
            }
        }
    }


}
