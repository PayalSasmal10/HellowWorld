pipeline {
	 environment {
	    registry = "payalsasmal/hello"
	    registryCredential = 'dockerhub'
	    app = ''
	    PROJECT_ID = 'rising-webbing-276809'
            CLUSTER_NAME = 'docker-image'
            LOCATION = 'us-central1-c'
            CREDENTIALS_ID = 'gke'

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
			     app = docker.build("registry:${env.BUILD_ID}")
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
			sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
			step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml',
			credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            	}



	    }


    }
}
