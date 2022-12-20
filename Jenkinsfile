pipeline {
    agent any
    environment {
        ECR_TOKEN = credentials('ecr-token')
    }
    stages {
        stage('Build') {
            steps {
                withMaven(maven : 'apache-maven-3.8.6') {
                                bat 'mvn clean install'
                 }
            }
        stage('Publish'){
            steps {
            }
        }
        stage('Run') {
            steps {
                bat 'docker images'
                bat 'docker run -t helloworld:1.0'
            }
        }
    }
}
