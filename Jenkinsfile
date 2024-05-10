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
		stage ("Testing the Build"){
			steps {
				sh 'sudo docker run -dit --name java-test$BUILD_TAG -p 8090:8080 gouravaas/app-java:$BUILD_TAG'
			}
		}
		stage ("QAT Testing"){
			steps {
				retry(5) {
					script {
						sh 'sudo curl --silent http://3.110.43.197:8090/java-web-app/ | grep -i -E "(india|sr)"'
					}
				}
			}
		}
		stage ("Approval from QAT"){
			steps {
				script {
					Boolean userInput = input(id: 'Proceed1', message: 'Do you want to Promote this build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                				echo 'userInput: ' + userInput
				}
			}
		}
		stage ("Prod ENV"){
			steps{
				sshagent(credentials:['cloud-slave']) {
			    	 	sh "ssh -o StrictHostKeyChecking=no ubuntu@65.0.131.149 sudo docker run  -dit  -p  :8080  gouravaas/app-java:$BUILD_TAG"
				}
			}
		}
			
	}

}
