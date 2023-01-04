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
        
        stage("Deploy") {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'webserver-pk', keyFileVariable: 'identity', usernameVariable: 'userName')]) {
                    script {
                        def remote = [:]
                        remote.name = "webserver-nginx"
                        remote.host = "ec2-3-75-207-119.eu-central-1.compute.amazonaws.com"
                        remote.allowAnyHosts = true
                        remote.user = userName
                        remote.identityFile = identity
                        
//                      This command fails when trying to ssh to Ubuntu 22 with an com.jcraft.jsch.JSchException: Auth fail
//                      The reason is that JSch is outdated and is not working with rsa-sha2 required by the server.
//                      https://stackoverflow.com/questions/73135640/jschexception-auth-fail-on-ubuntu-22-04
//                      Solution is to allow sha-rsa by adding PubkeyAcceptedAlgorithms=+ssh-rsa into /etc/ssh/sshd_config on the server.
//                      Or use a previous version of Ubuntu that accepts sha-rsa.
                        sshCommand remote: remote, command: 'sudo docker run -t public.ecr.aws/l9o2c9u6/helloworld:1.0'
                    }
                }
            }
        }
    }  
}



