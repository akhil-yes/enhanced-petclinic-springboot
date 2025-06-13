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
        stage('Deploy') {
            steps {
                echo "Deploy2"
            }
        }
    }
}