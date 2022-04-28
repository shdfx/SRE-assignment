pipeline {
    
    agent any
    
    environment {
        dockerImage = ''
        registry = 'shdfx/testimage'
        registryCredential = 'dockerhub-id'
    }
    
    stages {
        stage("checkout"){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/shdfx/test1']]])
            }
        }
    
        stage("Build Docker Image"){
            steps{
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage("Pushing Image") {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        
        stage("Running the container") {
            steps{
                script {
                    dockerImage.run("-p 8081:8081 --rm --name testContainer")
                }
            }
        }
    }
}
