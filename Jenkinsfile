pipeline {
  agent any

  environment {
    DOCKER_HUB_REPO = 'vyshnavi525/flask-app'
    IMAGE_TAG = "v${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Image') {
      steps {
        bat 'docker build -t %DOCKER_HUB_REPO%:%IMAGE_TAG% .'
      }
    }

    stage('Login to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          bat '''
          echo %PASS% | docker login -u %USER% --password-stdin
          '''
        }
      }
    }

    stage('Push Image') {
      steps {
        bat 'docker push %DOCKER_HUB_REPO%:%IMAGE_TAG%'
      }
    }

    stage('Tag & Push latest') {
      when { expression { return env.BRANCH_NAME == 'main' || env.GIT_BRANCH == 'main' } }
      steps {
        bat '''
        docker tag %DOCKER_HUB_REPO%:%IMAGE_TAG% %DOCKER_HUB_REPO%:latest
        docker push %DOCKER_HUB_REPO%:latest
        '''
      }
    }
  }

  post {
    success {
      echo "✅ Successfully pushed image to Docker Hub: ${env.DOCKER_HUB_REPO}:${env.IMAGE_TAG}"
    }
    always {
      bat 'docker logout || echo logout skipped'
    }
  }
}
