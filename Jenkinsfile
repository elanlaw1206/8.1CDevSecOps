pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonarcloud-token')  // Make sure this ID is correct
    }

    tools {
        nodejs 'NodeJS 20'  // Use the one you configured
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
                script {
                    try {
                        sh 'snyk test || true'
                    } catch (err) {
                        currentBuild.result = 'FAILURE'
                        throw err
                    }
                }
            }
            post {
                always {
                    emailext subject: "Build ${env.JOB_NAME} - #${env.BUILD_NUMBER} - TEST STAGE: ${currentBuild.currentResult}",
                             body: "Test stage completed with status: ${currentBuild.currentResult}\n\nCheck logs at: ${env.BUILD_URL}",
                             attachLog: true,
                             to: 'YOUR_EMAIL_HERE'
                }
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    try {
                        sh 'npm audit || true'
                    } catch (err) {
                        currentBuild.result = 'FAILURE'
                        throw err
                    }
                }
            }
            post {
                always {
                    emailext subject: "Build ${env.JOB_NAME} - #${env.BUILD_NUMBER} - SECURITY SCAN: ${currentBuild.currentResult}",
                             body: "Security scan stage completed with status: ${currentBuild.currentResult}\n\nCheck logs at: ${env.BUILD_URL}",
                             attachLog: true,
                             to: 'YOUR_EMAIL_HERE'
                }
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withSonarQubeEnv('SonarCloud') {
                    sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=elanlaw1206_8.1CDevSecOps \
                      -Dsonar.organization=elanlaw1206 \
                      -Dsonar.host.url=https://sonarcloud.io \
                      -Dsonar.token=${SONAR_TOKEN} || true
                    '''
                }
            }
        }

        stage('Deploy to Staging') {
            when { expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' } }
            steps {
                echo 'Deploying to staging...'
            }
        }

        stage('Integration Tests') {
            when { expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' } }
            steps {
                echo 'Running integration tests...'
            }
        }

        stage('Deploy to Production') {
            when { expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' } }
            steps {
                echo 'Deploying to production...'
            }
        }
    }

    post {
        failure {
            emailext subject: "BUILD FAILED: ${env.JOB_NAME} - #${env.BUILD_NUMBER}",
                     body: "The build failed. Check details at: ${env.BUILD_URL}",
                     attachLog: true,
                     to: 'YOUR_EMAIL_HERE'
        }
        success {
            emailext subject: "BUILD SUCCESS: ${env.JOB_NAME} - #${env.BUILD_NUMBER}",
                     body: "Build completed successfully!\nDetails at: ${env.BUILD_URL}",
                     to: 'YOUR_EMAIL_HERE'
        }
    }
}
