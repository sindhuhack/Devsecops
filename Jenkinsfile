pipeline {
  agent any 
  tools {
    maven 'maven'
  }
      stages {
      stage ('Check-Git-Secrets') {
   steps {
    sh 'rm trufflehog || true'
    sh 'docker run gesellix/trufflehog --json https://github.com/sindhuhack/Devsecops.git > trufflehog'
    sh 'cat trufflehog'
    }
}
      stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
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
