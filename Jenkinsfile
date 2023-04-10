pipeline {
    agent any
    tools {
        maven '_3_5_0'
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
                    sh 'docker build -t abhaykohli/devops_practice .'
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
        stage("Deploying to minikube"){
            steps{
                script{
                    // Deploy the Docker image to Minikube using the Kubernetes plugin
                    kubernetesDeploy(
                        kubeconfig: credentials('Minikube'),
                        configs: ['path/to/deployment.yml', 'path/to/service.yml'],
                        enableConfigSubstitution: true,
                        values: [
                            IMAGE_NAME: 'abhaykohli/devops_practice:latest'
                        ]
                    )
                }
            }
        }
    }
}
