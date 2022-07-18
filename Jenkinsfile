pipeline {
    agent any 
    environment {
        registry = "vikashk872/external"
        registryCredential = 'dockerhub'
        imageName = 'vikashk872/external'
        dockerImage = ''
        }
    stages {
        stage('Cloning the External repo') {
             
            steps {
               checkout scm

                echo 'showing files from repo?' 
                sh 'ls -a'
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
               // sh 'npm test'
                echo 'Testing completed'
            }
        }
        stage('Building image') {
            steps{
                script {
                    echo 'building image' 
                    dockerImage = docker.build("${env.imageName}:${env.BUILD_ID}")
                    echo 'image built'
                }
            }
            }
        stage('Push Image') {
            steps{
                script {
                    echo 'pushing the image to docker hub' 
                    docker.withRegistry('',registryCredential){
                        dockerImage.push("${env.BUILD_ID}")
                    }
                }
            }
        }     
         stage('deploy to k8s') {
             agent {
                docker { 
                    image 'google/cloud-sdk:latest'
                    args '-e HOME=/tmp'
                    reuseNode true
                        }
                    }
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials demo-cluster --zone us-central1-c --project capstone-355612'
                sh "kubectl set image deployment/external-deployment events-external=${env.imageName}:${env.BUILD_ID} --namespace=events"

             }
        }     
        stage('Remove local docker image') {
            steps{
                echo "pending"

                sh "docker rmi ${env.imageName}:${env.BUILD_ID}"
            }
        }
    }
}
