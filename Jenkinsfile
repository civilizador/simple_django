pipeline {
  
agent {
    kubernetes {
      label 'test-app'  // all your pods will be named with this prefix, followed by a unique id
      defaultContainer 'docker'  // define a default container if more than a few stages use it, will default to jnlp container
    }
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
        container('docker') { 
            sh "echo 'BUILD STAGE: ' "
            sh 'docker build -t civilizador/sample_django .'
        }
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