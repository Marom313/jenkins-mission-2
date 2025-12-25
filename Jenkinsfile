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

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker build -t ${IMAGE_NAME} ${SERVICE}
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                      echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                  docker push ${IMAGE_NAME}
                '''
            }
        }

        stage('Deploy with Ansible') {
            steps {
                sh '''
                  ansible-playbook -i inventory.ini deploy-playbook.yml \
                    --extra-vars "service=${SERVICE} image=${IMAGE_NAME} host_port=${HOST_PORT}"
                '''
            }
        }
    }
}
