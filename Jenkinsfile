pipeline {
    agent any

    stages {
        stage('SCM Checkout') {
            steps {
                git 'https://github.com/thedovekana/sample-cicd-project.git'
            }
        }
        stage('Build docker image') {
            steps {
                sh 'docker build -t notes-app .'
            }
        }
        stage('Pushing image to the dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub_credentials', passwordVariable: 'dockerhub_password', usernameVariable: 'dockerhub_username')]) {
                sh "docker tag notes-app ${dockerhub_username}/notes-app:v2 "
                sh "docker login -u ${dockerhub_username} -p ${dockerhub_password}"
                sh "docker push ${dockerhub_username}/notes-app:v2"
                    
                }
            }
        }
        
        stage('Deploy container') {
            steps {
              withCredentials([usernamePassword(credentialsId: 'ssh_credentials', passwordVariable: 'ssh_password', usernameVariable: 'ssh_username')]) {
                sh "sshpass -p '${ssh_password}' ssh -o StrictHostKeyChecking=no ${ssh_username}@172.31.28.230 'docker stop notes-app || true'"
                sh "sshpass -p '${ssh_password}' ssh -o StrictHostKeyChecking=no ${ssh_username}@172.31.28.230 'docker rm notes-app || true'"
                sh "sshpass -p '${ssh_password}' ssh -o StrictHostKeyChecking=no ${ssh_username}@172.31.28.230 'docker run -d -p 8000:8000 --name notes-app thedove/notes-app:v2'"
                }
            }
        }    
    }
}
