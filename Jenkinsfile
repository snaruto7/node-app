pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "apt-get update && apt-get upgrade"
                sh "apt-get install docker -y"
                sh "docker build . -t snaruto7/nodeapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u snaruto7 -p ${dockerHubPwd}"
                    sh "docker push snaruto7/nodeapp:${DOCKER_TAG}"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                script{
                   try{
                       sh "kubectl apply -f pods.yml"
                       sh "kubectl apply -f services.yml"
                   } catch(error){
                        sh "echo Error"
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
