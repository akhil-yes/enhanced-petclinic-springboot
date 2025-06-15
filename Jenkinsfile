pipeline {
    agent any
    tools{
        maven 'maven'
    }
    environment{
        ACR_NAME = "akhilcr"
        IMAGE_NAME = "springboot"
        IMAGE_TAG = "latest"
        TENANT_ID = "ad3ffba9-49d4-436d-a56a-148ba78fcabb"
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
        stage('Check Docker') {
            steps {
                sh 'docker --version'
                sh 'docker ps'
            }
        }
        stage('Docker Build') {
                steps {
                    script {
            echo 'Docker Build Started'
            sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                    } 
                }
        }
        stage ('Azure Login to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'acr-creds', usernameVariable: 'ACR_USERNAME', passwordVariable: 'ACR_PASSWORD')]) {
                    script {
                        echo 'Azure Login'
                        sh '''
                        az login --service-principal -u $ACR_USERNAME -p $ACR_PASSWORD --tenant $TENANT_ID
                        az acr login --name $ACR_NAME
                    '''
                    }
                }
            }
        }

    } 
}