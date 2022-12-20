pipeline {
    
    remote.name = "WebServer"
    remote.host = "3.72.11.182"
    remote.allowAnyHosts = true
    
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
        }
        
        stage('Publish') {
            steps {
                bat 'docker login --username AWS --password %ECR_TOKEN% public.ecr.aws/l9o2c9u6'
                bat 'docker tag helloworld:1.0 public.ecr.aws/l9o2c9u6/helloworld:1.0'
                bat 'docker push public.ecr.aws/l9o2c9u6/helloworld:1.0'
            }
        }
        
        stage('Run') {
            steps {
                bat 'docker images'
                bat 'docker run -t helloworld:1.0'
            }
        }
        
        stage('Deploy') 
        {
            withCredentials([sshUserPrivateKey(credentialsId: 'webserver-pk', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'ubuntu')]) 
            {
                remote.user = ubuntu
                remote.identityFile = identity
                steps {
                    sshCommand remote: remote, command: 'docker run --name helloworld public.ecr.aws/l9o2c9u6/helloworld:1.0'
                }
            }
        }
    }
}
