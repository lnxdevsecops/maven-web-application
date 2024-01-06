pipeline {
    agent any 
    environment {
        CUSTOM_BUILD_NUMBER = "${BUILD_NUMBER}"
        NEXUS_DOCKER_REPO = '13.201.135.167:9988'
    }
    tools {
        maven "Maven 3.8.5"
    }
    
    stages {
        stage("PullCodeFromSCM"){
            steps{
                git credentialsId: 'scm cred', 
                url: 'https://github.com/lnxdevsecops/maven-web-application.git',
                branch: 'master'
            }
        }
        stage ('Build') {
            steps {
                sh 'mvn clean package' 
            }

        }
        
        stage("GenerateSonarQubeReport"){
            steps{
                sh 'mvn sonar:sonar'
            }
        }
        
        stage("Build Docker Image"){
            steps{
                sh 'docker build -t 13.201.135.167:9988/maven-web-app:${CUSTOM_BUILD_NUMBER} .'
            }
        }
        

        stage("PussDockerImage into Docker Private Repo"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker private repo cred', passwordVariable: 'docker_repo_passwd', usernameVariable: 'docker_repo_user')]) {
                    sh "echo $docker_repo_passwd | docker login -u $docker_repo_user --password-stdin ${NEXUS_DOCKER_REPO}"
                    sh "docker push 13.201.135.167:9988/maven-web-app:${CUSTOM_BUILD_NUMBER}"
                 }  
            }
        } 
        
        stage("DployAppinK8S"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker private repo cred', passwordVariable: 'docker_repo_passwd', usernameVariable: 'docker_repo_user')]) {
                    sh "ssh -o StrictHostKeyChecking=no root@13.127.215.34  echo $docker_repo_passwd | docker login -u $docker_repo_user --password-stdin ${NEXUS_DOCKER_REPO}"
                 }  
                sh 'kubectl run maven-web-app --image  maven-web-app:${CUSTOM_BUILD_NUMBER}'
            }
            
        }
        
    }
}
