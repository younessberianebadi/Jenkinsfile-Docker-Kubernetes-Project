def remote = [:]
remote.name = 'K8S master'
remote.host = '10.10.11.112'
remote.user = 'younesbe'
remote.password = '0000'
remote.allowAnyHosts = true
pipeline {
    agent any
    tools { 
        maven 'M2_HOME' 
    }
    stages {
        stage("Git Clone"){

            steps {
                git branch: 'main', credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/younessberianebadi/frontend-service.git'
            }
        }
        stage("Maven build"){
            steps{
                sh 'mvn clean package'
            }
        }
        stage("Docker build"){
            steps{
                sh 'docker version'
                sh 'docker build -t spring-frontend .'
                sh 'docker image list'
                sh 'docker tag spring-frontend younessberianebadi/frontend-with-jenkins-pipeline:demo'
            }
        }
        stage("Docker login"){
            steps{
                withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
                    sh 'docker login -u younessberianebadi -p $PASSWORD'
                }
            }
        }
        stage("Push Image to Docker Hub"){
            steps{
                sh 'docker push younessberianebadi/frontend-with-jenkins-pipeline:demo'
            }
        }
        stage("SSH Into k8s Server"){
            stages{
                stage("Copying the K8s configuration file"){
                    steps{
                        sshPut remote: remote, from: 'deployment-service.yaml', into: '.'
                    }
                }
                stage("Deploying application on cluster"){
                    steps{
                        sshCommand remote: remote, command: "kubectl apply -f deployment-service.yaml"
                    }
                }
            }
        }
    }
}

// Run the following
// sudo usermod -a -G root jenkins
// sudo service jenkins restart
