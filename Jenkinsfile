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
        echo "Checkout..."
        checkout scm
      }
    }

    stage('Prepare Docker context') {
      steps {
        script {
          if (isUnix()) {
            sh '''
              docker context use desktop-linux || true
              docker buildx version || true
            '''
          } else {
            // Windows: ensure docker uses desktop-linux context
            bat '''
              docker context use desktop-linux || exit 0
              docker buildx version || exit 0
            '''
          }
        }
      }
    }

    stage('Build with buildx (explicit)') {
      steps {
        script {
          if (isUnix()) {
            sh 'docker buildx build --progress=plain --load -t $DOCKER_IMAGE .'
          } else {
            bat 'docker buildx build --progress=plain --load -t %DOCKER_IMAGE% .'
          }
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        script {
          if (isUnix()) {
            sh '''
              echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
              docker push $DOCKER_IMAGE
            '''
          } else {
            bat '''
              echo %DOCKERHUB_PASS% > pw.txt
              type pw.txt | docker login -u %DOCKERHUB_USER% --password-stdin
              docker push %DOCKER_IMAGE%
              del pw.txt
            '''
          }
        }
      }
    }
  }
  post {
    always {
      echo 'Pipeline finished (success or fail)'
    }
  }
}
