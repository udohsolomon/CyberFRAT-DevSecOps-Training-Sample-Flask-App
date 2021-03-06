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
        sh "docker run dxa4481/trufflehog:latest --json https://github.com/udohsolomon/CyberFRAT-DevSecOps-Training-Sample-Flask-App.git > trufflehog.json || true"
        sh "cat trufflehog.json"
      }
   }
    
    stage('SCA') {
      steps {
        sh "pip3 install safety"
        sh "rm -rf safety.json || true"
        sh "safety check -r requirements.txt --json https://github.com/deepshankaryadav/CyberFRAT-DevSecOps-Training-Sample-Flask-App.git > safety.json || true"
        sh "cat safety.json"
      }
    }
    
    stage('SAST') {
      steps {
        sh "rm -rf bandit.json || true"
        sh "bandit -r -f json -o bandit.json . || true"
        sh "cat bandit.json"
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
    
    
    
