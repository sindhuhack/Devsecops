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
         sh 'wget "https://raw.githubusercontent.com/sindhuhack/Devsecops/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
      stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
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
      stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@34.233.30.229 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://3.234.189.32:8090/webapp/" || true'
        }
      }
    }
   }
}
