pipeline {
	 environment {
	    registry = "payalsasmal/1strepository"
	    registryCredential = 'dockerhub'
	    app = ''
	    PROJECT_ID = 'rising-webbing-276809'
        CLUSTER_NAME = 'docker-image'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'My First Project'

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
                     app = docker.build registry + "$BUILD_NUMBER"
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
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' services.yml deployment.yml "
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', 'services.yml',
                credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }



	    }


    }
}
