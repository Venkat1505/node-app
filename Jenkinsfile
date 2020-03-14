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
        stage('DockerHub Push'){
            steps{
         withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
         sh "docker login -u veparala -p ${dockerHubPwd}"
         sh "docker push veparala/nodeapp:${DOCKER_TAG}"
         }
            }

        }
        stage('Deploy to K8S')
        steps{
            sh "chmod +x changeTag.sh"
            sh "./changeTag.sh ${DOCKER_TAG}"
            sshagent(['kops-machine']){
                sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@10.0.1.98:/home/ec2-user/"
                script{
                    try{
                        sh "ec2-user@10.0.1.98 kubectl apply -f ."
                    }catch(error){
                        sh "ec2-user@10.0.1.98 kubectl create -f ."
                    }
                }

            }

        }
       
     }
}


def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}

