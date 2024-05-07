pipeline {
	agent any
	stages {
		stage (pull code from git repo){
			steps{
				git branch: 'main', url: 'https://github.com/gouravaas/app-java.git'
			}
		}
		stage (Build the code){
			steps{
				sh 'sudo mvn dependency:purge-local-repository'
                                sh 'sudo mvn clean package'
			}
		}
		stage (Building docker image){
			steps{
				sh 'docker build -t app-java:$BUILD_TAG .'
			}
		}
	}

}
