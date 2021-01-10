pipeline {
  environment {
    registry = "augeos/devsecops"
    registryCredential = "DockerHub"
    dockerImage = ''
  }
  
  agent any
  
  stages {
    stage('Check for Secrets') {
      steps {
        sh "rm -rf trufflehog.json || true"
        sh "docker run dxa4481/trufflehog:latest --json https://github.com/udohsolomon/CyberFRAT-DevSecOps-Training-Sample-Flask-App.git > trufflehog.json"
        sh "cat trufflehog.json"
      }
    }
    
    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    
    stage('Push to DockerHub') {
      steps {
        script {
          docker.withRegistry('', registryCredential ) {
            dockerImage.push('latest')
          }
        }
      }
    }
    
    stage('Deploy Test Application') {
      steps {
        sh 'docker stop flaskr && docker rm flaskr || true'
        sh 'docker pull augeos/devsecops:latest'
        sh 'docker run -d -p 5000:5000 --name flaskr augeos/devsecops:latest'
      }
    }
  }
}
    
    
    
