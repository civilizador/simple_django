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
  - name: docker
    image: docker:20.10-dind
    volumeMounts:
      - name: dockersock
        mountPath: "/var/run/docker.sock"
    command:
    - /bin/ash
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: alpine/k8s:1.24.12
    command:
    - cat
    tty: true
  volumes:
  - name: dockersock
    hostPath:
        path: "/var/run/docker.sock"
"""
}
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('docker-hub')
    DOCKER_PASSWORD = "${docker_password}"
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
            container('docker') {
                sh "echo 'BUILD STAGE: ' "
                sh 'docker build -t civilizador/sample_django .'
            }
        }
    }
    stage('Login') {
        steps {
            container('docker') {
                sh "echo 'LOGIN STAGE: ' "
                sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
            }
        }
            
    }
    stage('Push') {
      steps {
        container('docker') {
            sh "echo 'PUSH STAGE: ' "
            sh 'docker push civilizador/sample_django'
        }
      }
    }

    stage('Deploy Production') {
      // Production branch
      when { 
        anyOf { 
          branch 'main' 
          branch 'master' 
        }
      }
      steps{
        container('kubectl') {
        // Change deployed image in canary to the one we just built
          sh("helm repo add qalita-helm https://qalita-io.github.io/helm")
          sh("helm install my-argocd qalita-helm/argocd --version 5.22.1")
        }
      }
    }

  }
}