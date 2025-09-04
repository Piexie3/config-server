pipeline{
	agent { label 'jenkins-agent'}
	tools {
		jdk 'java21'
		maven 'maven3'
	}
	environment{
        GITHUB_REPO= credentials('GITHUB_REPO')
        APP_NAME = "config-server",
        RELEASE = "v1.0.0",
        DOCKER_USER= credentials('DOCKER_USER')
        DOCKER_PASS=credentials('DOCKER_PASS')
        IMAGE_NAME= "${DOCKER_USER}" + "/"+ "${APP_NAME}"
        IMAGE_TAG="${RELEASE}-${BUILD_NUMBER}"
        DOCKER_IMAGE_URL="${IMAGE_NAME}:latest"
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
        stage("Build and Push Docker Image"){
            steps{
                script {
                    docker.withRegistry('',DOCKER_PASS){
                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                    docker.withRegistry('',DOCKER_PASS){
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
	}
}