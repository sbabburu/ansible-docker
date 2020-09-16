pipeline{
    agent any
    tools {
       maven 'maven3'
     }
    environment {
        DOCKER_TAG = getVersion()
     }
    stages{
        
        stage('Clone'){
            
            steps{
                
                git credentialsId: 'github', url: 'https://github.com/sbabburu/ansible-docker.git'
            }
            
        }
        
        stage('maven-build'){
            
            steps{
                
                sh 'mvn clean package'
            }
            
        }
        
        stage('docker-build'){
            
            steps{
                
                sh 'docker build . -t sbabburu/santoshapp:${DOCKER_TAG}'
            }
            
        }
        
        stage('docker-push'){
            
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubpwd')]) {
                  sh 'docker login -u sbabburu -p ${dockerhubpwd}'    
              
              }
                sh 'docker push sbabburu/santoshapp:${DOCKER_TAG}'
                
            }
            
        }
        /*stage('docker-deploy'){
            
            steps{
                
                ansiblePlaybook credentialsId: 'Managed-Node', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
            
        }*/
        
        stage('k8's-deploy'){
            
            steps{
                
                kubernetesDeploy(
                  kubeconfigId: 'kubeconfig',
                  configs: 'kube-deploy.yml',
                  enableConfigSubstitution: true
                  
            }
            
        }
        
    }
    
    
}



def getVersion(){
    def latestVersion = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return latestVersion
}
