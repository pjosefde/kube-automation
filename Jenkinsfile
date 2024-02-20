def dockerRepoUrl = "localhost:5000"
def dockerImageName = "shubhambmatere/devops-integration"
//def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:latest"
  
pipeline {
    agent any
    tools{
        maven 'maven-3.9.4'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/pjosefde/kube-automation.git']])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t shubhambmatere/devops-integration .'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                //script{
                   //withCredentials([string(credentialsId: 'dockerhubpasswrd', variable: 'dockerhubpasswrd')]) {
                   //sh 'docker login -u shubhambmatere -p ${dockerhubpasswrd}'

            //}
                   //sh 'docker push shubhambmatere/devops-integration'
                   echo "Docker Image Tag Name: ${dockerImageTag}"

                   // sh "docker login -u admin -p admin123 ${dockerRepoUrl}"
                   sh "docker tag ${dockerImageName} ${dockerImageTag}"
                   sh "docker push ${dockerImageTag}"
                }
        }
        stage('Deploy to k8s'){
            steps {
                withKubeConfig([credentialsId: 'kube-master', serverUrl: 'https://192.168.1.34:6443']) {
                    sh 'kubectl delete -f deploymentservice.yaml'
                    sh 'kubectl apply -f deploymentservice.yaml'
                }
            }
        }
    }
}
