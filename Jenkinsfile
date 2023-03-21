pipeline {
  agent {
        docker { image 'node:7-alpine' }
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
  
    // stage('Install Docker') {
    //   steps {
    //     sh 
    //     sh 'curl -fsSL https://get.docker.com -o get-docker.sh'
    //     sh 'sudo sh get-docker.sh'
    //   }
    // }
    stage('Build') {
      steps {
            sh "echo 'BUILD STAGE: ' "
            sh 'docker build -t civilizador/sample_django .'
      }
    }
    stage('Login') {
      steps {
            sh "echo 'LOGIN STAGE: ' "
            sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
        }
    }
    stage('Push') {
      steps {
            sh "echo 'PUSH STAGE: ' "
            sh 'docker push civilizador/sample_django'
        }
    }
  }
}