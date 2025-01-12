pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'emrekardaslar/basic-kubernetes-app:latest'
        DOCKER_CREDENTIALS = 'docker-hub-credentials' // Match the ID from Step 3
        KUBECONFIG_FILE = 'kubeconfig' // Match the ID from Step 3
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/emrekardaslar/BasicKubernetesApp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        service docker start
                        docker build -t ${DOCKER_IMAGE} .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([file(credentialsId: KUBECONFIG_FILE, variable: 'KUBECONFIG')]) {
                        sh """
                        export KUBECONFIG=$KUBECONFIG
                        kubectl set image deployment/basic-kubernetes-app basic-kubernetes-app=${DOCKER_IMAGE} -n default
                        kubectl rollout restart deployment/basic-kubernetes-app -n default
                        """
                    }
                }
            }
        }
    }
}
