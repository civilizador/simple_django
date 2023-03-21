pipeline {
    agent any
    environment {
        PROJECT_ID = "${DJANGO_PROJECT_ID}"
        CLUSTER_NAME = "${CLUSTER_NAME}"
        LOCATION = "${LOCATION}"
        CREDENTIALS_ID = "master-sql-379304"
        DOCKER_PASSWORD = "${docker}"
        DOCKER_USERNAME = "${docker_username}"
        DOCKER_REGISTRY = "civilizador/sample_django"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            agent {
                docker {
                    image 'node:16.13.1-alpine'
                    reuseNode true
                }
            }
            steps {
                // Build the Docker image
                sh "docker build -t ${env.DOCKER_REGISTRY}:latest ."
            }
        }
        
        stage('Push') {
            steps {
                // Log in to the Docker registry
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                }

                // Push the Docker image to the registry
                sh "docker push ${env.DOCKER_REGISTRY}:latest"
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
