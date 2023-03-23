pipeline {
  
  agent {
    kubernetes {
      label 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: cd-jenkins
  containers:
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
}
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('docker-hub')
    DOCKER_PASSWORD = "${docker}"
    DOCKER_USERNAME = "${docker_username}"
  }

  
  stages {
    stage('Checkout') {
            steps  {
                checkout scm
            }
        }
  
    stage('Build') {
        steps {
            container('gcloud') {
                sh "echo 'BUILD STAGE: ' "
                sh 'docker build -t civilizador/sample_django .'
            }
        }
    }
    stage('Login') {
        steps {
            container('gcloud') {
                sh "echo 'LOGIN STAGE: ' "
                sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
            }
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