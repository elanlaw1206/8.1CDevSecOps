pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/elanlaw1206/8.1CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
    }

    stage('Generate Coverage Report') {
      steps {
        sh 'npm run coverage || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true'
      }
    }
    
    stage('SonarCloud Analysis') {
        steps {
            withSonarQubeEnv('SonarCloud') {
                def scannerHome = tool 'SonarScanner' 
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
    }


    stage('Deploy to Staging') {
      steps {
        echo 'Simulating deployment to staging environment (e.g., AWS EC2)'
      }
    }

    stage('Integration Tests on Staging') {
      steps {
        echo 'Running integration tests on staging...'
      }
    }

    stage('Deploy to Production') {
      steps {
        echo 'Deploying to production server...'
      }
    }
  }
}
