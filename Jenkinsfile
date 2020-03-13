pipeline {
    agent any
    environment{

        DOCKER_TAG = getDockerTag()
    }
     stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t veparala/nodeapp:${DOCKER_TAG}"
            }
        }
        stage('Dockerhub Push'){
         withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
         sh "docker login -u veparala -p ${dockerHubPwd}"
         sh "docker push veparala/nodeapp:${DOCKER_TAG}"
         }

        }
       
     }
}


def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}

