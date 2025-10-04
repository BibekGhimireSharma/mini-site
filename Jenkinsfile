pipeline {
  agent any
  environment {
    // Docker image name with your username
    DOCKER_IMAGE = "vivek3001/mini-site:latest"
    // Jenkins credentials we'll create later
    DOCKERHUB_USER = "vivek3001"
    DOCKERHUB_PASS = credentials('dockerhub-creds')
  }
  stages {
    stage('Checkout') {
      steps {
        echo "Pulling code from GitHub..."
        checkout scm
      }
    }
    stage('Build Docker Image') {
      steps {
        echo "Building Docker image..."
        sh 'docker build -t $DOCKER_IMAGE .'
      }
    }
    stage('Push to Docker Hub') {
      steps {
        echo "Pushing image to Docker Hub..."
        sh '''
          echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
          docker push $DOCKER_IMAGE
        '''
      }
    }
  }
  post {
    always {
      echo 'Pipeline finished successfully!'
    }
  }
}
