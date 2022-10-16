pipeline {
    agent any
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')
    }

    tools {
        maven 'maven 3.8.6'
    }
    parameters {
        choice choices: ['development', 'master'], name: 'BranchName'
    }

    stages {
        stage('gitHub') {
            steps {
                git credentialsId: 'GitHub', url: 'https://github.com/SecondOne2516/maven-web-application'
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
}
