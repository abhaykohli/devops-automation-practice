pipeline {
    agent any
    tools {
        maven '_3_5_0'
    }
    environment {
        IMAGE_NAME = "abhaykohli/devops_practice"
        IMAGE_TAG = "latest"
        CLUSTER_NAME = "DevopsProject"
    }
    stages {
        stage('Build maven') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/abhaykohli/devops-automation-practice']])
                sh 'mvn clean install'
            }
        }
        stage('Build Docker image') {
            steps {
                script {
                    sh 'docker build -t abhaykohli/devops_practice -t practice .'
                }
            }
        }
        stage("Pushing to hub"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhubpaswd', variable: 'dockerhubpaswd')]) {
                    sh 'docker login -u abhaykohli -p ${dockerhubpaswd}'
                    sh 'docker push abhaykohli/devops_practice:latest'
                }               
            }
        }
    }
        stage("Deploying to EKS"){
            steps{
                script{
                     withAWS(region: 'ap-south-1', credentials: 'AWSCREDS') {
                     sh "aws eks update-kubeconfig --name ${CLUSTER_NAME}"
                     sh "kubectl apply -f Deployment.yml"
                     sh "kubectl apply -f Service.yml"
                }   
                }
            }
        }
    }
}
