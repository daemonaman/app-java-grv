pipeline {
	agent any
	stages {
		stage ("pull code from git repo"){
			steps{
				git branch: 'main', url: 'https://github.com/gouravaas/app-java.git'
			}
		}
		stage ("Build the code"){
			steps{
				sh 'sudo mvn dependency:purge-local-repository'
                                sh 'sudo mvn clean package'
			}
		}
		stage ("Building docker image"){
			steps{
				sh 'sudo docker build -t app-java:$BUILD_TAG .'
				sh 'sudo docker tag app-java:$BUILD_TAG gouravaas/app-java:$BUILD_TAG '
			}
		}
		stage ("Push on Docker-Hub"){
		        steps{
				withCredentials([string(credentialsId: 'docker_hub_passwd', variable: 'docker_hub_passwd_var')]) {
                                	sh 'sudo docker login -u gouravaas -p ${docker_hub_passwd_var}'
					sh 'sudo docker push gouravaas/app-java:$BUILD_TAG'
					}

			}
   		}	
			
	}

}
