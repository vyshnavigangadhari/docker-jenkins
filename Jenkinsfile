pipeline {
  agent any

  environment {
    DOCKER_HUB_REPO = 'vyshnavi525/flask-app'
    IMAGE_TAG = "v${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build Image') {
      steps { bat 'docker build -t %DOCKER_HUB_REPO%:%IMAGE_TAG% .' }
    }

    stage('Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          bat '''
          echo %PASS% | docker login -u %USER% --password-stdin
          docker push %DOCKER_HUB_REPO%:%IMAGE_TAG%
          docker logout
          '''
        }
      }
    }
  }

  post {
    success {
      echo "✅ Image pushed to Docker Hub: ${env.DOCKER_HUB_REPO}:${env.IMAGE_TAG}"
    }
  }
}
