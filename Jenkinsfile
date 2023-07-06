pipeline {
    agent any
        environment {
            AWS_ACCOUNT_ID="912647749713"
            AWS_DEFAULT_REGION="us-west-1"
            CLUSTER_NAME="default"
            SERVICE_NAME="python-container-service"
            TASK_DEFINITION_NAME="first-run-task-definition"
            DESIRED_COUNT="1"
            IMAGE_REPO_NAME="demo"
            IMAGE_TAG="${env.BUILD_ID}"
            REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
            registryCredential = "demo-admin-user"
    }
    
    stages {
        

        stage ('Test'){

             steps {
                 sh 'python testRoutes.py'
                }
        }
        
        stage('Build Image') {

            steps {
                script {
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"

                }
            }
        }

        stage('Push To ECR') {
   
            steps {
			    docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
                }
            }
        }
                    
        stage('Deploy') {

           steps {
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            }   
              
           }
        }

      }
      
