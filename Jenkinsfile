pipeline {
    agent any
    environment {
        PROJECT_ID = "${DJANGO_PROJECT_ID}"
        CLUSTER_NAME = "${CLUSTER_NAME}"
        LOCATION = "${LOCATION}"
        CREDENTIALS_ID = "master-sql-379304"
        DOCKER = "${docker}"
        DOCKER_USER = "${docker_username}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build image') {
            steps {
                script {
                    app = docker.build("arshad1914/pipeline:${env.BUILD_ID}")
                    }
            }
        }
        
        stage('Push image') {
            steps {
                script {
                    { sh "docker login -u "${env.DOCKER_USER}" -p ${docker}" }
                    app.push("${env.BUILD_ID}")
                 }
                                 
            }
        }
    
        stage('Deploy to K8s') {
            steps{
                echo "Deployment started ..."
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/pipeline:latest/pipeline:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', \
                  projectId: env.PROJECT_ID, \
                  clusterName: env.CLUSTER_NAME, \
                  location: env.LOCATION, \
                  manifestPattern: 'deployment.yaml', \
                  credentialsId: env.CREDENTIALS_ID, \
                  verifyDeployments: true])
                }
            }
        }    
}
