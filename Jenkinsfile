pipeline {
    agent any
    environment {
        registry = "633346295112.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo"
    }

    triggers {
        githubPush() // Triggers on GitHub commit
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/pdpatil4472/mega-project-2.git']])
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }
        
        stage('Push Image to ECR') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 633346295112.dkr.ecr.us-east-1.amazonaws.com'
                    dockerImage.push("${BUILD_NUMBER}")
                }
            }
        }
        
        stage('Deploy Helm') {
            steps {
                script {
                    sh 'helm upgrade --install first mychart --namespace helm-deployment --set image.tag=${BUILD_NUMBER}'
                    sh 'pwd'
                    sh 'ls -la'
                }
            }
        }
    }
}
