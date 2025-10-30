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
        sh 'docker build -t $DOCKER_HUB_REPO:$IMAGE_TAG .'
      }
    }

    stage('Login to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'echo $PASS | docker login -u $USER --password-stdin'
        }
      }
    }

    stage('Push Image') {
      steps {
        sh 'docker push $DOCKER_HUB_REPO:$IMAGE_TAG'
      }
    }

    stage('Tag & Push latest') {
      when { expression { return env.BRANCH_NAME == 'main' || env.GIT_BRANCH == 'main' } }
      steps {
        sh '''
          docker tag $DOCKER_HUB_REPO:$IMAGE_TAG $DOCKER_HUB_REPO:latest
          docker push $DOCKER_HUB_REPO:latest
        '''
      }
    }
  }

  post {
    success {
      echo "✅ Pushed successfully to Docker Hub: $DOCKER_HUB_REPO:$IMAGE_TAG"
    }
    always {
      sh 'docker logout || true'
    }
  }
}
