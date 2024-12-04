node('agent-slave'){
     
    stage('SCM Checkout'){
        git url: 'https://github.com/Danle360/maven-web-application.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven-3.6.0", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Build Docker Image'){
        sh 'docker build -t danle360/java-web-app .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'docker_pwd', variable: 'docker_pwd')]) {
          sh "docker login -u danle360 -p ${docker_pwd}"
        }
        sh 'docker push danle360/java-web-app'
     }
     
      stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app danle360/java-web-app'
         
         sshagent(['DOCKER_SERVER']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@3.20.226.135 docker stop java-web-app || true'
          sh 'ssh  ubuntu@3.20.226.135 docker rm java-web-app || true'
          sh 'ssh  ubuntu@3.20.226.135 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ubuntu@3.20.226.135 ${dockerRun}"
       }
       
    }
     
     
}
