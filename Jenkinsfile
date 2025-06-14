pipeline {
    agent any
    tools{
        maven 'maven'
    }
    environment{
        IMAGE_NAME = "springboot"
        IMAGE_TAG = "latest"
    }
    stages {
        stage('Checkout From Git') {
            steps {
                git branch: 'main', url: 'https://github.com/akhil-yes/enhanced-petclinic-springboot.git'
            }
        }
        stage('Maven Compile') {
            steps {
                echo 'This is Maven Compile Stage'
                sh 'mvn compile'
            }
        }
        stage('Maven Package') {
            steps {
                echo 'This is Maven Package Stage'
                sh 'mvn package'
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    echo 'Docker Build Started'
                    docker.build ("$IMAGE_NAME:$IMAGE_TAG")
                }
                
            }
        }
       
    }
}