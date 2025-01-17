pipeline {
    agent any
    tools {
        maven 'maven'
    }
    
    stages {
        stage('Pulling from git hub'){
            steps{
                echo'Pulling code from git hub'
                git credentialsId: 'Git_MainCred', url: 'https://github.com/Faozil/web-app.git'
                echo 'Pulling done'
            }
        }
        
        stage('Build with Maven'){
            steps{                 
                echo 'Building with Maven'
                sh 'mvn clean install'
                echo 'Building done'
            }
        }
        stage('Test with Sonarqube'){
            steps{
                 script {
                    sh 'docker run --name sonar1 -d -p 9000:9000 sonarqube:latest'
                    sh 'sleep 60'
                 }
                echo 'Testing with Sonarqube'
                sh 'mvn sonar:sonar'
                echo 'Testing done'
            }
        }
        stage('Upload to Nexus'){
            steps{
                 script {
                    sh 'docker run --name nexus1 -d -p 8081:8081 sonatype/nexus3:3.56.0'
                    sh 'sleep 240'
                }
                echo 'Uploading to Nexus'
                sh 'mvn deploy'
                echo 'Uploading done'
            }
        }
        stage('Build Docker Image'){
            steps{
                echo 'Building Docker Image'
                sh 'docker rm -f my-app'
            //  sh 'docker rmi -f faozil/my-web-app:latest' 
                sh 'docker build -t faozil/my-web-app:1.0.0 .'
                echo 'Docker Image built'
            }
        }
        stage('Push Docker Image to Dockerhub'){
            steps{
                withCredentials([string(credentialsId: 'faozil', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u faozil -p ${dockerhubpwd}'  
                    sh 'docker push faozil/my-web-app:1.0.0'
                }
            }
        }
        stage('Deploy to Tomcat'){
            steps{
                echo 'Running a Tomcat Container off my-web-app base image'
                sh 'docker run --name my-app -d -p 1000:8080 faozil/my-web-app:1.0.0'
                echo 'Deployment done'
            }
        }
    }

    /*
        stage('deploy'){
            sshagent(['tomcat']) {
                sh "scp -o StrictHostKeyChecking=no target/*war ec2-user@54.174.107.138:/opt/tomcat9/webapps/"
            }
        }
       
    */

}
