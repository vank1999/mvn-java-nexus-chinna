pipeline {
    agent { label 'slave' }
    tools{
        maven 'maven-3.9'
    }
    stages {
        stage('git checkout') {
            steps {
                git url: 'https://github.com/vank1999/mvn-java-nexus-chinna.git', branch: 'main'
            }
        }
         stage('build maven application') {
            steps {
                sh 'mvn clean package'
            }
        }
          stage('genetrate sonarqube-analysis') {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube', credentialsId: 'sonar-token') {
                 sh 'mvn sonar:sonar'
                }
            }
        }

        stage('upload war file to nexus'){
            steps{
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'hiring',
                         classifier: '',
                          file: 'target/hiring.war',
                           type: 'war'
                           ]
                           ], 
                           credentialsId: 'nexus-credentials',
                           groupId: 'in.javahome', 
                           nexusUrl: '52.207.226.37:8081', 
                           nexusVersion: 'nexus3', 
                           protocol: 'http', 
                           repository: 'chinna-app', 
                           version: '0.1'
            }
        }

        stage('deploy war file to tomcat'){
            steps{
                sshagent(['tomcat-credentials']) {
                sh """
                    scp -o StrictHostKeyChecking=no target/hiring.war ubuntu@54.89.32.17:/opt/tomcat/webapps
                    ssh -o StrictHostKeyChecking=no ubuntu@54.89.32.17 'ls -l /opt/tomcat/webapps/hiring.war'
                    ssh -o StrictHostKeyChecking=no ubuntu@54.89.32.17 /opt/tomcat/bin/shutdown.sh
                    ssh -o StrictHostKeyChecking=no ubuntu@54.89.32.17 /opt/tomcat/bin/startup.sh
                   """

                }
            }
        }

    }
}
