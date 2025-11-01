cat > jenkins/Jenkinsfile <<'EOF'
pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build Docker Image') {
      when { branch 'staging' }
      steps {
        sh 'docker build -t angelbista/chatbot-ui:staging -f Chatbot-UI/Dockerfile Chatbot-UI'
      }
    }
    stage('Push to Docker Hub') {
      when { branch 'staging' }
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
          sh 'docker push angelbista/chatbot-ui:staging'
        }
      }
    }
    stage('Optional: Deploy to K8s') {
      when { branch 'staging' }
      steps {
        // requires Jenkins to have kubeconfig configured
        sh 'kubectl apply -f k8s/ --namespace production || true'
      }
    }
  }
}
EOF
