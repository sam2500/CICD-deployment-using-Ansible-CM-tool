pipeline {
    agent any
	tools {
    	maven 'Maven_Home'
        jdk 'JDK_11'
	}
    stages {
        stage('Initialize Git POLL SCM Feature') {
            steps {
                //Enable remote triggers
                script {
                    properties([[$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/bipin115/CICD-deployment-using-Ansible-CM-tool.git'], pipelineTriggers([pollSCM('* * * * *')])])
                }  
            }
        }

	stage('Git project checkout') {
        steps {
               git branch: 'master', url: 'https://github.com/bipin115/CICD-deployment-using-Ansible-CM-tool.git'  
          }
        }
        
        stage(' Applying Playbook to Configurig tomcat server on remote server'){
            steps {
                sh "ansible-playbook tomcat-setup.yaml"
            }
	}
		
	stage(' Building WAR package from Java project'){
            steps {
                build 'deploy-war-package-remote-host'
            }
	    post {
                success {
                    echo "Now Archiving the Artifacts...."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
		
	stage(' Deployig WAR package on remote tomcat server'){
            steps {
                //deploy adapters: [tomcat9(credentialsId: 'tomcat-admin-deployment', path: '', url: 'http://13.233.154.169:8080/')], contextPath: '/', onFailure: false, war: 'spring-maven-app-0.0.1-SNAPSHOT.war'
		scp -P 80 spring-maven-app-0.0.1-SNAPSHOT.war devops@15.207.98.117:/opt/tomcat/webapps
            }
        }
    }
}
