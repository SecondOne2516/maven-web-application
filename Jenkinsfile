node {
    try {
        notifyBuild('Started')
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: '']])
    def MAVEN_HOME= tool name: "maven 3.8.6" 
    stage('gitHub') {
        git credentialsId: 'GitHub', url: 'https://github.com/SecondOne2516/maven-web-application'
    }
    stage('Maven') {
        sh "${MAVEN_HOME}/bin/mvn clean package"
    }
    stage('SonarQubeReport') {
        sh "${MAVEN_HOME}/bin/mvn sonar:sonar"
    }
    stage('Nexus') {
        sh "${MAVEN_HOME}/bin/mvn deploy"
    }
    stage('TomcatServer') {
        sshagent(['d736ca28-b458-4fc6-ac65-24fdf4a0fe73']) {
            sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.3.155:/opt/apache-tomcat-9.0.68/webapps/"
        }
         
    }
    }
    catch(e) {
        currentBuild.result = "FAILED"
            throw e
    }
    finally {
        notifyBuild(currentBuild.result)
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
