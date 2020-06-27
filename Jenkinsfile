pipeline {
  agent any 
  tools {
    maven 'maven'
  }
      stages {
      stage ('Check-Git-Secrets') {
   steps {
    sh 'rm trufflehog || true'
    sh 'docker run gesellix/trufflehog --json https://github.com/sindhuhack/testing.git > trufflehog'
    sh 'cat trufflehog'
    }
}

    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
           steps {
          sshagent(['JRNTR']) {
               sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@3.234.189.32:/opt/apache-tomcat-8.5.56/webapps/webapp.war'
             }      
          }       
   }
}
}
