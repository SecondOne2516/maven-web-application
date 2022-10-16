pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')
    }

    tools {
        maven 'maven 3.8.6'
    }
    parameters {
        choice choices: ['development', 'master', 'qa'], description: 'Select the branch', name: 'BranchName'
    }

    stages {
        stage('gitHub') {
            steps {
                notifyBuild('STARTED')
                git branch: "${params.BranchName}", credentialsId: 'GitHub', url: 'https://github.com/SecondOne2516/maven-web-application'
            }
        }
        stage('Maven') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('SonarQubeReport') {
            steps {
                sh "mvn sonar:sonar"
            }
        }
        stage('Nexus') {
            steps {
                sh "mvn deploy"
            }
        }
        stage('TomcatServer') {
            steps {
                sshagent(['d736ca28-b458-4fc6-ac65-24fdf4a0fe73']) {
                    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.3.155:/opt/apache-tomcat-9.0.68/webapps/"
                }
            }
        }
    }
    post {
  success {
    notifyBuild('SUCCESSFUL')
  }
}

}
def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
