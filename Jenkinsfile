pipeline {
  agent any
  tools {
    maven 'maven3'
  }
  options {
    buildDiscarder logRotator(daysToKeepStr: '10', numToKeepStr: '7')
  }
  parameters {
  choice choices: ['develop', 'qa', 'master'], description: 'choice the branch to build', name: 'BranchName'
}
  stages {
    stage("git clone") {
      steps {
        git branch: 'develop', url: 'https://github.com/sirisatya8/SirishaDev.git'
      }
    }
    stage("Maven build") {
      steps {
        sh 'mvn clean package'
      }
    }
    stage("Deploy to Tomcat") {
      steps {
        sshagent(['tomcat-dev']) {
          sh "scp -o StrictHostKeyChecking=no target/*.war ec2-user@172.31.2.163:/opt/tomcat8/webapps/app.war"
          sh "ssh ec2-user@172.31.2.163 /opt/tomcat8/bin/startup.sh"
          sh "ssh ec2-user@172.31.2.163 /opt/tomcat8/bin/shutdown.sh"
        }
      }
    }
  }
  post {
    success {
      archiveArtifacts artifacts: 'target/*.war'
      cleanWs()
    }
  }
}
