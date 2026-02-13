pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "bkkrish007/devops-ecom-site:latest"
    EC2_HOST = "23.23.30.82"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Push Image') {
      steps {
        sh """
        export PATH=/usr/local/bin:\$PATH
        docker buildx build --platform linux/amd64 \
        -t ${DOCKER_IMAGE} \
        --push .
        """
      }
    }

    stage('Deploy to EC2') {
      steps {
        sshagent(['devops-key']) {   // <-- MATCH YOUR CREDENTIAL ID
          sh """
          ssh -o StrictHostKeyChecking=no ubuntu@${EC2_HOST} "
            docker pull ${DOCKER_IMAGE} &&
            docker rm -f ecom || true &&
            docker run -d -p 80:3000 --name ecom ${DOCKER_IMAGE}
          "
          """
        }
      }
    }
  }

  post {
    success { echo "🚀 Deployment Successful" }
    failure { echo "❌ Build Failed" }
  }
}