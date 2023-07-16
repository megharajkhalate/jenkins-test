pipeline {
    agent any
        environment {
            AWS_ACCOUNT_ID="912647749713"
            AWS_DEFAULT_REGION="us-west-1"
            CLUSTER_NAME="demo"
            SERVICE_NAME="python-service"
            TASK_DEFINITION_NAME="test"
            DESIRED_COUNT="1"
            IMAGE_REPO_NAME="demo2"
            IMAGE_TAG="${env.BUILD_ID}"
            REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
            registry_credential = "demo-admin-user"
    }
    
    stages {
        

        stage('Build Image') {

            steps {
                script {
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"

                }
            }
        }

        stage('Push To ECR') {
   
            steps {
		    script{
			docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
		    }
                }
            }
                    
        stage('Deploy') {

           steps {
            	withAWS(credentials: "${registry_credential}", region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            }   
              
           }
        }

      }
}
