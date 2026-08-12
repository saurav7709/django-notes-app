  pipeline {
      agent { label 'docker-agent' }
  
      environment {
          DOCKERHUB_USER = 'saaurav'
          IMAGE_NAME = 'notes-app'
          IMAGE_TAG = 'latest'
      }
  
      stages {
          stage('Code Clone') {
              steps {
                  echo "Cloning code from GitHub..."
                  git url: 'https://github.com/saurav7709/django-notes-app.git', branch: 'main'
                  echo "Code cloned successfully!"
              }
          }
          stage('Build Docker Image') {
              steps {
                  echo "Building Docker image..."
                  sh '''
                      rm -rf data/
                      docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} .
                  '''
                  echo "Image built successfully!"
              }
          }
          stage('Push to Docker Hub') {
              steps {
                  echo "Pushing image to Docker Hub..."
                  withCredentials([usernamePassword(
                      credentialsId: 'dockerhub-cred',
                      usernameVariable: 'DOCKER_USER',
                      passwordVariable: 'DOCKER_PASS'
                  )]) {
                      sh '''
                          echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                          docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                          docker logout
                      '''
                  }
                  echo "Image pushed to Docker Hub!"
              }
          }
          stage('Deploy') {
              steps {
                  echo "Deploying application..."
                  sh '''
                      docker-compose down || true
                      docker-compose up -d
                  '''
                  echo "App deployed!"
              }
          }
      }
  
      post {
          success {
              echo "CI/CD Pipeline completed! Image pushed & app deployed."
          }
          failure {
              echo "Pipeline failed! Check logs above."
          }
      }
  }
