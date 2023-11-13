node {
  def mavenHome= tool name: "maven3.8.5"
  stage("CheckOutCode"){
    git credentialsId: 'github cred', url: 'https://github.com/lnxdevsecops/maven-web-application.git'
  }
  stage("BuildPackage"){
    sh "${mavenHOme}/bin/mvn clean package"
  }
  stage("GenerateSonarQubeReport"){
    sh "${mavenHome}/bin/mvn sonar:sonar"
  }
  stage("PushArtifactsIntoNexusRepo"){
    sh "${mavenHome}/bin/mvn clean deploy"
  }
  stage("DeployAppintoServer"){
    sshagent(['tomcat_cred']) {
       scp -o StrickHostKeyChecking=no  target/*.war  ec2-user@18.212.131.66:/opt/tomcat/webapps/
    }
  }
  
}
