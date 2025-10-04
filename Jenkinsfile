pipeline {
  agent any
  environment {
    DOCKER_IMAGE = "vivek3001/mini-site:latest"
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
        script {
          if (isUnix()) {
            sh 'docker build -t $DOCKER_IMAGE .'
          } else {
            // Windows: use bat to run docker
            bat 'docker build -t %DOCKER_IMAGE% .'
          }
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        echo "Pushing image to Docker Hub..."
        script {
          if (isUnix()) {
            sh '''
              echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
              docker push $DOCKER_IMAGE
            '''
          } else {
            // Windows: use bat and windows variables
            // create a temp login file with password and use it to login (works on Windows)
            bat """
              echo %DOCKERHUB_PASS% > pw.txt
              type pw.txt | docker login -u %DOCKERHUB_USER% --password-stdin
              docker push %DOCKER_IMAGE%
              del pw.txt
            """
          }
        }
      }
    }
  }

  post {
    always {
      echo 'Pipeline finished (success or fail).'
    }
  }
}
