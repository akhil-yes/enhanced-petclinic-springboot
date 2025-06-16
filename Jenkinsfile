pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        ACR_NAME = "akhilcr.azurecr.io"
        SERVICE_PRINCIPLE = "azapp"
        IMAGE_NAME = "springboot"
        IMAGE_TAG = "${BUILD_NUMBER}"
        TENANT_ID = "ad3ffba9-49d4-436d-a56a-148ba78fcabb"
        FULL_IMAGE_NAME = "${ACR_NAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        RESOURCE_GROUP = "RG"
        AKS_CLUSTER_NAME = "myAKSCluster"
        ACR_CREDENTIALS = credentials('acr-creds')
        AZURE_CREDENTIALS = credentials('azure-sp')
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
                        echo $ACR_PASSWORD | docker login $ACR_NAME -u $ACR_USERNAME --password-stdin
                    '''
                }
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
         stage('Azure Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-sp', usernameVariable: 'AZURE_CLIENT_ID', passwordVariable: 'AZURE_CLIENT_SECRET')]) {
                    sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant ad3ffba9-49d4-436d-a56a-148ba78fcabb'
                }
            }
        }
         stage('AKS Get Credentials') {
            steps {
                sh 'az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER_NAME'
            }
        }
         stage('AKS Deploy or Update') {
            steps {
                script {
                    def deploymentExists = sh(script: "kubectl get deployment springboot-app --ignore-not-found", returnStatus: true) == 0

                    if (deploymentExists) {
                        sh 'kubectl set image deployment/springboot-app springboot-app=$ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG'
                    } else {
                        sh 'kubectl create deployment springboot-app --image=$ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG'
                        sh 'kubectl expose deployment springboot-app --type=LoadBalancer --port=8080'
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl rollout status deployment/springboot-app'
                sh 'kubectl get svc springboot-app'
            }
        }
       

    }
}
