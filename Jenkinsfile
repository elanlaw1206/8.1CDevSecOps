pipeline {
    agent any

    tools {
        nodejs 'NodeJS 20'
    }

    environment {
        EMAIL_RECIPIENT = 'elanlaw1206@gmail.com'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test > test.log || true'
            }
            post {
                always {
                    emailext subject: "Test Stage: ${currentBuild.fullDisplayName} - ${currentBuild.currentResult}",
                             body: "Test stage completed. See attached log.",
                             attachLog: true,
                             to: "${EMAIL_RECIPIENT}"
                }
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running security scan...'
                sh 'npm audit > audit.log || true'
            }
            post {
                always {
                    emailext subject: "Security Scan: ${currentBuild.fullDisplayName} - ${currentBuild.currentResult}",
                             body: "Security scan completed. See attached audit log.",
                             attachmentsPattern: 'audit.log',
                             to: "${EMAIL_RECIPIENT}"
                }
            }
        }
    }

    post {
        failure {
            emailext subject: "Pipeline FAILED: ${currentBuild.fullDisplayName}",
                     body: "Build failed. Check Jenkins console or attachments for details.",
                     attachLog: true,
                     to: "${EMAIL_RECIPIENT}"
        }
    }
}
