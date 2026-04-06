pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = "lalitaly"
        APP_NAME = "node-hello-world-jenkin"
    }
    stages {
        stage('Build Image') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USER}/${APP_NAME}:${BUILD_NUMBER} ."
            }
        }
        stage('Push to Registry') {
            steps {
                // Pre-requisite: Add DockerHub credentials to Jenkins with ID 'dockerhub-creds'
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/${APP_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        stage('Deploy to K8s') {
            steps {
                // Update the image version in the YAML file
                sh "sed -i 's|latest|${BUILD_NUMBER}|g' k8s/deployment.yaml"
                sh "kubectl apply -f k8s/deployment.yaml"
                sh "kubectl apply -f k8s/service.yaml"
            }
        }

    }
}
