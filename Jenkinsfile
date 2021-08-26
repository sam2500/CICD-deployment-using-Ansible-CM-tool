pipeline {
    agent any
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
               git branch: 'main', url: 'https://github.com/bipin115/CICD-deployment-using-Ansible-CM-tool.git'  
          }
        }
        
        stage('Build Application') {
            steps {
                sh 'mvn -f CICD-deployment-using-Ansible-CM-tool/pom.xml clean package'
            }
            post {
                success {
                    echo "Now Archiving the Artifacts...."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage(' Applying Playbook to Configurig tomcat server on remote server'){
            steps {
                sh "su -i"
                sh "su - devops"
                sh "ansible-playbook tomcat-setup.yaml"
            }
        }
    }
}
