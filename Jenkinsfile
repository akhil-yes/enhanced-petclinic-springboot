pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        ACR_NAME = "akhilcr"
        IMAGE_NAME = "springboot"
        IMAGE_TAG = "latest"
        TENANT_ID = "ad3ffba9-49d4-436d-a56a-148ba78fcabb"
        FULL_IMAGE_NAME = "akhilcr.azurecr.io/springboot:latest"
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
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }
        stage('ACR Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'acr-creds', usernameVariable: 'ACR_USERNAME', passwordVariable: 'ACR_PASSWORD')]) {
                    sh '''
                        echo $ACR_PASSWORD | docker login $ACR_NAME.azurecr.io -u $ACR_USERNAME --password-stdin
                    '''
                }
            }
        }
        stage('Build Docker Image for ACR') {
            steps {
                sh '''
                    docker build -t $ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }
        stage('Push to ACR') {
            steps {
                sh '''
                    docker push $ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Docker Push') {
            steps {
                script {
                     echo 'Docker Push Started'
                        sh '''
                            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                            docker push ${FULL_IMAGE_NAME}
                     '''
                }
            }
        }
    }
}
