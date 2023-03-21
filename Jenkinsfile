pipeline {
  agent { docker 'docker' }
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('docker-hub')
    DOCKER_PASSWORD = "${docker}"
    DOCKER_USERNAME = "${docker_username}"
  }
  stages {
    stage('Checkout') {
            steps {
                checkout scm
            }
        }
    stage('Build') {
      steps {
        sh 'docker build -t civilizador/sample_django .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker push civilizador/sample_django'
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}