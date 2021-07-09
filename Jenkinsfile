pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
            echo "PATH = ${PATH}"
            echo "M2_HOME = ${M2_HOME}"
        ''' 
      }
    }

  stage ('Check-Git-Secrets') {
      steps {
        // sh 'sudo usermod -aG docker jenkins' #add this cmd to vm and restart jenkins
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/opeomotayo/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/opeomotayo/webapp/master/owasp-dependency-check.sh" '
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
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war vagrant@172.42.42.102:/home/linuxbrew/.linuxbrew/Cellar/tomcat@8/8.5.69/libexec/webapps/webapp.war'
              }      
           }       
    }

  }
}