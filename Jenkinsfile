pipeline{
	agent { label 'jenkins-agent'}
	tools {
		jdk 'java21'
		maven 'maven3'
	}
	environment{
        GITHUB_REPO= credentials('GITHUB_REPO')
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
        stage("Sonarqube analysis"){
            steps{
                script {
                    withSonarQubeEnv( credentialsId: "jenkins-sonarqube-token"){
                        dir('config-server') {
                            sh "mvn sonar:sonar"
                        }
                    }
                }
            }
        }
        stage("Quality gate"){
            steps{
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: "jenkins-sonarqube-token"
                }
            }
        }
	}
}