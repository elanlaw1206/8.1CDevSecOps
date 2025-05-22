pipeline {
    agent any

    environment {
        // SonarCloud Configuration
        SONARQUBE_ENV = 'SonarCloud'
        // If you set SONAR_TOKEN as a Jenkins secret credential, expose it here
        SONAR_AUTH_TOKEN = credentials('sonar-token')
        // Optional: extend PATH if node/npm installed in a non-default location
        PATH = "/usr/local/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/elanlaw1206/8.1CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // Snyk must already be authenticated in the container or globally
                sh 'snyk test || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=elanlaw1206_8.1CDevSecOps \
                          -Dsonar.organization=elanlaw1206 \
                          -Dsonar.host.url=https://sonarcloud.io \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Deploy to Staging') {
            when { expression { false } } // Disable for now
            steps {
                echo 'Deploying to staging...'
            }
        }

        stage('Integration Tests') {
            when { expression { false } }
            steps {
                echo 'Running integration tests...'
            }
        }

        stage('Deploy to Production') {
            when { expression { false } }
            steps {
                echo 'Deploying to production...'
            }
        }
    }
}
