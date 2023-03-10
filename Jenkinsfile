pipeline {
    agent any
        tools {
               maven 'Maven'
        }

        environment {
        PROJECT_ID = 'devops-374608'
        CLUSTER_NAME = 'cluster-1'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'devops'
        }

    stages {
        stage('pull docker image form dockerhub') {
            steps {
                sh 'docker pull masudd11/django-gunicorn:slim01'
                sh 'docker tag masudd11/django-gunicorn:slim01 gcr.io/devops-374608/django-gunicorn:${BUILD_NUMBER}'
            }    
        }
        stage("Push Docker Image GCR registry") {
		    steps {
                echo 'pushing image to gcr'
				withDockerRegistry(credentialsId: 'gcr:devops', url: 'https://gcr.io') {
                sh  'docker push gcr.io/devops-374608/django-gunicorn:${BUILD_NUMBER}'
		        }
		    }
	    }
    
        stage('Deploy to GKE') {
            steps{
                echo "deploying on kubernetes cluster"
                sh 'pwd'   
                sh "sed -i 's/tagversion/${BUILD_NUMBER}/g' pyapp.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'pyapp.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        } 

    }
}