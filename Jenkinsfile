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
       
        stage(' Clean existing workspace') {
        steps {
               cleanWs() 
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
                deploy adapters: [tomcat9(credentialsId: 'tomcat-admin-remote-deployment', path: '', url: 'http://3.109.200.185:8080/')], contextPath: '/', war: '**/*.war'
		//sh "scp /var/lib/jenkins/workspace/deploy-war-package-remote-host/target/spring-maven-app-0.0.1-SNAPSHOT.war devops@15.207.98.117:/opt/tomcat/webapps/"   
            }
        }
    }
}
