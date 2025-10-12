pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE = "your_dockerhub_username/hello-app"
        AWS_REGION = "ap-south-1"
    }
    triggers { pollSCM('H/5 * * * *') }

    stages {
        stage('Checkout') {
            steps { git branch: 'main', url: 'https://github.com/yourusername/hello-app.git' }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."
                sh "docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest"
            }
        }

        stage('Security Scan - Trivy') {
            steps { sh "trivy image --exit-code 1 --severity HIGH,CRITICAL ${DOCKER_IMAGE}:${BUILD_NUMBER} || true" }
        }

        stage('Push to Docker Hub') {
            steps {
                sh """
                echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                docker push ${DOCKER_IMAGE}:latest
                docker logout
                """
            }
        }

        stage('Deploy via Helm') {
            steps {
                sh "aws eks update-kubeconfig --region ${AWS_REGION} --name hello-eks-cluster"
                sh "helm upgrade --install hello-app ./helm/hello-app --set image.tag=${BUILD_NUMBER}"
            }
        }

        stage('Verify Deployment') {
            steps {
                sh "kubectl rollout status deployment/hello-app"
            }
        }
    }

    post {
        success { echo "✅ Deployment successful!" }
        failure { echo "❌ Build or deploy failed." }
        always { cleanWs() }
    }
}

