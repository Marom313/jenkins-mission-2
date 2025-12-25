pipeline {
  agent any

  parameters {
    choice(
      name: 'SERVICE',
      choices: ['service1', 'service2'],
      description: 'Which service to deploy'
    )
    string(
      name: 'HOST_PORT',
      defaultValue: '8080',
      description: 'Host port to expose'
    )
  }

  environment {
    DOCKERHUB_USER = 'marom313'
    IMAGE_TAG = "${BUILD_NUMBER}"
    IMAGE_NAME = "${DOCKERHUB_USER}/${SERVICE}:${IMAGE_TAG}"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Docker Login') {
      steps {
        withCredentials([
          usernamePassword(
            credentialsId: 'dockerhub-creds',
            usernameVariable: 'DOCKERHUB_USER',
            passwordVariable: 'DOCKERHUB_PASS'
          )
        ]) {
          sh '''
            echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
          '''
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          docker build -t ${IMAGE_NAME} ${SERVICE}
        '''
      }
    }

    stage('Push Image') {
      steps {
        sh '''
          docker push ${IMAGE_NAME}
        '''
      }
    }
  }
}

