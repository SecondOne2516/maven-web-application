pipeline {
    agent any
    parameters {
        choice choices: ['development', 'master', 'QA'], description: 'Select the branch', name: 'BranchName'
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')
    }

    triggers {
        pollSCM '* * * * *'
    }
    tools {
        maven 'maven 3.8.6'
    }
    stages {
        stage('gitHub') {
            steps {
                git branch: '${params.BranchName}', git credentialsId: 'GitHub', url: 'https://github.com/SecondOne2516/maven-web-application'
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
