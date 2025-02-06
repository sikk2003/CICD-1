pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }
    stages {
        stage('Build') {
            steps {
                checkout scmGit(branches: [[name: '*/Dev']], extensions: [], userRemoteConfigs: [[credentialsId: 'ssa-git-jenkins', url: 'https://github.com/sikk2003/CICD-1.git']])
                sh 'npm install'
                // sh 'npm run build'
            }
        }
        stage('Test') {
            steps {
                // sh 'npm run test'
                echo "Test"
            }
        }
       stage('Build Image') {
            steps { 
                sh 'docker build -t reactimage .'
                sh 'docker tag reactimage:latest sikk2003/dev:latest'
            }    
       }
       stage('Docker login') {
            steps { 
                withCredentials([usernamePassword(credentialsId: 'ssa-docker-ID', passwordVariable: 'docker-hub-token', usernameVariable: 'USER')]) {
                sh "echo $PASS | docker login -u $USER --password-stdin"
                sh 'docker push sikk2003/dev:latest'
                }
            }
       }
       stage('Deploy') {
            steps {  
                script {
                   def dockerCmd = 'docker run -itd --name My-first-container -p 80:5000 sikk2003/dev:latest'
                   sshagent(['ssa-aws-ubuntu']) {
                   sh "ssh -o StrictHostKeyChecking=no ubuntu@52.207.216.239 ${dockerCmd}"
                   }
                }
            }
       }
    }
}
