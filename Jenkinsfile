pipeline {
  agent any
  environment {
    AWS_ACCESS_KEY_ID = 'AWS_ACCESS_KEYID'
    AWS_SECRET_ACCESS_KEY = 'AWS_SECRET_ACCESS_KEY'
    REGION = 'us-east-1'
    CLUSTER_NAME = 'nodeapp'  
    SERVICE_NAME = 'nodeapp-svc'
    REPO_NAME = 'jay-nodejs' 
    IMAGE_TAG = "${BUILD_NUMBER}" 
    IMAGE_URI = "860005315103.dkr.ecr.${REGION}.amazonaws.com/${REPO_NAME}:${IMAGE_TAG}"
  }
  stages {
    
    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/mrraj1/ecs.git'        
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          
          sh 'docker build -t $REPO_NAME:$IMAGE_TAG .'
        }
      }
    }

    stage('Push Docker Image to ECR') {
      steps {
        script {
          
          sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 860005315103.dkr.ecr.us-east-1.amazonaws.com'
          sh 'docker tag $REPO_NAME:$IMAGE_TAG $IMAGE_URI'
          sh 'docker push $IMAGE_URI'
        }
      }
    }

    stage('Deploy to ECS') {
      steps {
        script {
          
          sh """
            aws ecs update-service --cluster $CLUSTER_NAME \
              --service $SERVICE_NAME \
              --force-new-deployment \
              --region $REGION
          """
        }
      }
    }
  }
}
