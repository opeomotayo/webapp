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