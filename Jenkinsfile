pipeline {
    agent any
    tools{
        maven 'maven'
    }
    stages {
        stage('Checkout From Git') {
            steps {
                git branch: 'main', url: 'https://github.com/akhil-yes/enhanced-petclinic-springboot.git'
            }
        }
        stage('Maven Compile') {
            steps {
                echo "This is maven compile stage"
                sh "mvn compile"
            }
        }
       stage('Maven Test') {
            steps {
                echo "This is maven test stage"
                sh "mvn test"
            }
        }
         stage('File scanning by Trivy') {
            steps {
                echo "Trivy Scanning"
                sh "trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL ."
            }
        }
    }
}