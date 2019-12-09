environment {
    registry = "docker_hub_account/repository_name"
    registryCredential = 'dockerhub'
}
node{
  stage('SCM Checkout'){
    checkout scm
  }
    
   //Unit Test
  stage('Maven Test'){
    def mvnHome = tool name: 'Maven-test', type: 'maven'
    sh "${mvnHome}/bin/mvn clean test"
  }
  //Build
  stage('Maven Compile'){
    def mvnHome = tool name: 'Maven-test', type: 'maven'
   //sh "${mvnHome}/bin/mvn package"
   // sh "${mvnHome}/bin/mvn clean install -DskipTests"
     sh "${mvnHome}/bin/mvn clean package -DskipTests"
  }
 
 //Build Docker image
   stage('Build Docker Image') {
    app = docker.build("lduardo/docker-jenkins")
  }
  //Push Image to Dockerhub lduardo
   stage('Push Image to Registry') {
   def dockerImage
   docker.withRegistry('https://registry-1.docker.io/v2/', 'dockerhub') {
    app.push 'master'
    //app.push "latest"
    }
  }
  //Deploy image in a remote server
   stage('Deploy Image') {
       def dockerstopApp = 'docker stop java-app'
       def dockerRun = 'docker run -d -p 8080:8080 --name=java-app lduardo/docker-jenkins:master'
       def dockerclean = 'docker system prune -f'
       sshagent(['app-server']) {
           sh "ssh -o StrictHostKeyChecking=no ubuntu@18.216.11.102 ${dockerstopApp}"
           sh "ssh -o StrictHostKeyChecking=no ubuntu@18.216.11.102 ${dockerclean}"
           sh "ssh -o StrictHostKeyChecking=no ubuntu@18.216.11.102 ${dockerRun}"
       }
       stage('Performance Test'){
    def mvnHome = tool name: 'Maven-test', type: 'maven'
     sh "${mvnHome}/bin/mvn clean verify"
  }  
       
}
}  
    
