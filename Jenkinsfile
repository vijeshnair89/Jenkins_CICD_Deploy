pipeline {
    agent any
    environment {
        mavenHome = tool name: "Maven-3.9.6",type: "maven"
        mavenCMD = "${mavenHome}/bin/mvn"
    }
    stages {
        stage('Fetch Code') {
            steps {
                git credentialsId: 'Github_credential', url: 'https://github.com/vijeshnair89/MavenBuild.git'
            }
        }
        stage('Maven Build') {
            steps {
               sh '${mavenCMD} clean package'
               
            }
        }
        stage('Code review') {
            steps {
              withSonarQubeEnv('sonar-server') {
                sh '${mavenCMD} sonar:sonar'
              }
            }
        }
        stage('Upload Artifact') {
            steps {
              nexusArtifactUploader artifacts: [[artifactId: 'java-example', classifier: '', file: 'target/java-example.war', type: 'war']], credentialsId: 'nexus-creds', groupId: 'in.vijesh', nexusUrl: '184.73.146.198:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'vijesh-repo', version: '1.0-SNAPSHOT'
            }
        }
        stage('Move Code to Tomcat') {
            steps {
                sshagent(['Tomcat-Server']) {
                    sh 'scp -o StrictHostKeyChecking=no target/java-example.war  ubuntu@3.80.64.59:/home/ubuntu'
                    
                }
            }
        }
        stage('Deploy code') {
            agent { label 'node1' }
            steps {
                sh 'sudo mv /home/ubuntu/java-example.war /var/lib/tomcat9/webapps'
            }
        }

    }
}
