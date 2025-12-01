pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "rafkaihza78/hello-cicd"
        DOCKER_TAG   = "latest"
        KUBECONFIG   = "/var/jenkins_home/kube-jenkins.config"
    }

    // 🔔 Trigger lewat GitHub Webhook
    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                  docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                  kubectl apply -f k8s.yaml
                  kubectl rollout restart deployment hello-cicd-deployment
                """
            }
        }
    }
}
