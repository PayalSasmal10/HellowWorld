pipeline {
	 environment {
	    registry = "payalsasmal/1strepository"
	    registryCredential = 'dockerhub'
	    app = ''
	    DOCKER_TAG = getDockerTag()

  	}
	agent any
	    stages {
	        stage('Clone Repository') {
	        steps {
	        checkout scm
	        }
	   }
	   stage('Build Docker Image') {
	        steps {
				script {
				 app = docker.build registry:${DOCKER_TAG}
				 app.inside {
					sh 'echo $(curl localhost:8888)'
				 }
				}
	        }
	   }

	   
	   stage('Push Docker image') {
	        steps {
                   script {
		            docker.withRegistry( '', registryCredential ) {
            	       app.push()
			        }
	               }
	   
	        }
	   }

	   stage('Deploy to K8S') {
	        steps{
	            sh "chmod +x changeTag.sh"
	            sh "./changeTag.sh ${DOCKER_TAG}"
	            sshagent(['docker-image']) {
	                sh "scp -o StrictHostKeyChecking=no services.yml hello-app-pods.yml princekr700@35.226.32.230:/home/princekr700/"
	                script{
	                    try{
                            sh "ssh princekr700@35.226.32.230 kubectl apply -f ."
	                    }catch(error){
                            sh "ssh princekr700@35.226.32.230 kubectl create -f ."
	                    }

	                }
	            }

	        }



	   }


    }
}

def getDockerTag(){
    def tag = sh script:'git rev-parse HEAD', returnStdout: true
    return tag
}