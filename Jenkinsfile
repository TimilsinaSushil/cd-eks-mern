pipeline {
  agent any


  stages {
    stage('Cleanup Workspace'){
            steps {
                script {
                    cleanWs()
                }
            }
        }
    stage('Checkout CD Repo') {
      steps {
        git credentialsId: 'github', 
        url: 'https://github.com/TimilsinaSushil/DEVOPS-MERN.git',
        branch: 'main'
      }
    }

    stage('Update Kubernetes Manifests') {
      steps {
        script {
          sh """
            cat k8s/frontend-deployment.yaml
            cat k8s/backend-deployment.yaml
            sed -i 's|mern-frontend:.*|mern-frontend:${IMAGE_TAG}|' k8s/frontend-deployment.yaml
            sed -i 's|mern-backend:.*|mern-backend:${IMAGE_TAG}|' k8s/backend-deployment.yaml
            cat k8s/frontend-deployment.yaml
            cat k8s/backend-deployment.yaml
          """
        }
      }
    }

    stage('Commit & Push Updated Manifests') {
        steps {
            withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
            sh """
                git config user.name "${GIT_USER}"
                git config user.email "${GIT_USER}@users.noreply.github.com"
                git add k8s/*.yaml
                git commit -m "Update image tags to ${IMAGE_TAG}"
                git push https://${GIT_USER}:${GIT_TOKEN}@github.com/TimilsinaSushil/mern-cd-eks.git HEAD:main
            """
            }
        }
    }
  }

  post {
    success {
      echo "Deployment to EKS successful!"
    }
    failure {
      echo "Deployment failed.Please check the logs."
    }
  }
}
