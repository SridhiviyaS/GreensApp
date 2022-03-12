pipeline{
    agent any 
    tools {
        maven 'maven'
    }
    environment {
        DOCKER_TAG = getVersion()
    }
    stages{
        stage("SCM checkoout") {
            steps{
                git 'https://github.com/SridhiviyaS/dockeransiblejenkins'
                }
        }
        stage("Maven Build") {
            steps{
                sh 'mvn clean package'
                }
        }
        stage("Docker Build") {
            steps{
                sh "docker build . -t greensclass/sampleapp:${DOCKER_TAG} ."
                }
        }
        stage("DockerHub Push") {
            steps{
                withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerhubPass')]) {
                    sh "docker login  -u greensclass -p ${dockerhubPass}"
                }
                
                sh "docker push greensclass/sampleapp:${DOCKER_TAG}"
                }
        }
        stage("DOCKER webserver install") {
            steps{
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, installation: 'Ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
                }
        }

       
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash

}

