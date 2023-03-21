pipeline {
  
  agent none

  environment {
    DOCKERHUB_CREDENTIALS = credentials('docker-hub')
    DOCKER_PASSWORD = "${docker}"
    DOCKER_USERNAME = "${docker_username}"
  }
  stages {
    stage('Checkout') {
    agent {
            docker { image 'docker:23.0.1-git' }
            }
            steps {
                checkout scm
            }
        }
  
    stage('Build') {
    agent {
            docker { image 'docker:23.0.1-git' }
            }
      steps {
            sh "echo 'BUILD STAGE: ' "
            sh 'docker build -t civilizador/sample_django .'
      }
    }
    stage('Login') {
    agent {
            docker { image 'docker:23.0.1-git' }
            }
      steps {
            sh "echo 'LOGIN STAGE: ' "
            sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
        }
    }
    stage('Push') {
    agent {
            docker { image 'docker:23.0.1-git' }
            }
      steps {
            sh "echo 'PUSH STAGE: ' "
            sh 'docker push civilizador/sample_django'
        }
    }
  }
}