pipeline {
    agent any
    tools{
        maven 'maven3.8.6'
    }
    environment{     
    DOCKERHUB_CREDENTIALS=credentials('dockerhubpwd')     
}
    stages{
        stage('Build with Maven') {
            steps{
                echo 'Building with Maven'
                git branch: 'main', credentialsId: 'Git_Cred', url: 'https://github.com/Bestedeki/Jenkins-Docker-K8s-Spring-boot-mongo.git'
                sh 'mvn clean package'
                echo 'Building done'
            }
        }
        stage('Slack Notification') {
            steps{
                echo 'Sending slack notification'
                slackSend channel: '#general', message: 'Build Success, kindly review and approve for further processing'
                echo 'Notification sent'
            }
        }
        stage('Build Docker Image') {
            steps{
                echo 'Building Docker Image'
                sh 'docker build -t bestedeki/spring-boot-mongo .'
                echo 'Docker image built'
                
            }
        }
        stage('Approval') {
            steps{
                echo 'Seeking approval'
                timeout(time: 5, unit: "DAYS"){
                input message: 'Approve to production'
                }
            }
        }
        
        stage('Push Image to Dockerhub') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin' 
                sh 'docker push bestedeki/spring-boot-mongo'
            }
        }
        stage('Kubernetes Deployment'){
            steps{
                echo 'Deploying to Kubernetes cluster'
                sh 'kubectl apply -f springBootMongo.yml'
                echo 'Deployment done'
            }
        }
    }
}
