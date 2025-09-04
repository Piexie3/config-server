pipeline{
	agent { label 'jenkins-agent'}
	tools {
		jdk 'java21'
		maven 'maven3'
	}
	environment{
        GITHUB_REPO= "https://github.com/Piexie3/hekopay-core.git"
	}

	stages{
		stage("Cleanup Workspace"){
			steps {
				cleanWs()
			}
		}
		stage("Check out from SCM"){
			steps {
                git branch: 'main', credentialsId: 'github', url: "${GITHUB_REPO}"
			}
		}
		stage("build application"){
			steps {
			    dir('config-server') {
				    sh "mvn clean package"
                }
			}
		}
	}
}