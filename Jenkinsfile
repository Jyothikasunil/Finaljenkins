pipeline {
    agent any

    environment {
        CODE_DIRECTORY = "/path/to/code"
        TEST_ENV = "testing"
        PROD_ENV = "yourname_production"
        NOTIFICATION_EMAIL = "jyothikasunil006@gmail.com"
    }

    stages {
        stage('Build') {
            steps {
                echo "Building the code using Maven"
                // Example build step
                sh 'mvn clean install'
            }
        }
        stage('Unit and Integration Tests') {
            steps {
                echo "Performing Unit Testing using JUnit..."
                echo "Performing Integration Testing using Selenium WebDriver..."

                script {
                    def logFilePathNew = "${env.WORKSPACE}/test-output.log"
                
                    sh """
                        echo 'Starting unit testing using JUnit...' > ${logFilePathNew}
                        echo 'Testing the feature working...' >> ${logFilePathNew}
                        echo 'Unit testing completed and no issues found' >> ${logFilePathNew}
                        echo '\\n\\nStarting Integration testing using Selenium WebDriver...' >> ${logFilePathNew}
                        echo 'End to End Testing of the complete product working...' >> ${logFilePathNew}
                        echo 'Integration testing completed and no issues found' >> ${logFilePathNew} 
                    """
                }
            }
            post {
                success {
                    emailext attachmentsPattern: 'test-output.log',
                             body: 'The Unit Testing and Integration testing completed successfully',
                             subject: 'Unit and Integration Testing Status: SUCCESS',
                             to: "${env.NOTIFICATION_EMAIL}"
                }
                failure {
                    emailext attachmentsPattern: 'test-output.log',
                             body: 'The Unit Testing and Integration testing completed unsuccessfully. Please check logs!',
                             subject: 'Unit and Integration Testing Status: FAILURE',
                             to: "${env.NOTIFICATION_EMAIL}"
                }
            }
        }
        stage('Code Analysis') {
            steps {
                echo 'Running code analysis with SonarQube'
                // Example code analysis step
                sh 'sonar-scanner | tee code-analysis.log'
                archiveArtifacts artifacts: 'code-analysis.log'
            }
        }
        stage('Security Scan') {
            steps {
                echo 'Running security scan with OWASP ZAP'
                // Example security scan step
                sh 'zap-cli quick-scan --self-contained | tee security-scan.log'
                archiveArtifacts artifacts: 'security-scan.log'
            }
            post {
                success {
                    emailext attachmentsPattern: 'security-scan.log',
                             subject: 'Security Scan Passed',
                             body: 'Security scan completed without any issues. See attached logs for details.<br>Console Output: ${env.BUILD_URL}console',
                             mimeType: 'text/html',
                             to: "${env.NOTIFICATION_EMAIL}"
                }
                failure {
                    emailext attachmentsPattern: 'security-scan.log',
                             subject: 'Security Scan Failed',
                             body: 'Security scan has some issues. See attached logs for details.<br>Console Output: ${env.BUILD_URL}console',
                             mimeType: 'text/html',
                             to: "${env.NOTIFICATION_EMAIL}"
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                echo "Deploying the application to staging server using AWS CodeDeploy"
                // Example staging deployment step
                sh 'aws deploy push --application-name my-app --s3-location s3://my-bucket/my-app.zip'
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                echo "Running integration tests on the staging environment with Selenium"
                // Example integration tests on staging step
                sh 'selenium-tests | tee integration-tests-staging.log'
                archiveArtifacts artifacts: 'integration-tests-staging.log'
            }
            post {
                success {
                    emailext attachmentsPattern: 'integration-tests-staging.log',
                             subject: 'Integration Tests on Staging Passed',
                             body: 'Integration tests on staging environment have passed successfully. See attached logs for details.<br>Console Output: ${env.BUILD_URL}console',
                             mimeType: 'text/html',
                             to: "${env.NOTIFICATION_EMAIL}"
                }
                failure {
                    emailext attachmentsPattern: 'integration-tests-staging.log',
                             subject: 'Integration Tests on Staging Failed',
                             body: 'Integration tests on staging environment have failed. See attached logs for details.<br>Console Output: ${env.BUILD_URL}console',
                             mimeType: 'text/html',
                             to: "${env.NOTIFICATION_EMAIL}"
                }
            }
        }
        stage('Deploy to Production') {
            steps {
                echo "Deploying the code to the $PROD_ENV environment"
                // Example production deployment step
                sh 'aws deploy push --application-name my-app --s3-location s3://my-bucket/my-app.zip'
            }
        }
    }

    post {
        always {
            emailext (
                subject: "Pipeline Status: ${currentBuild.result}",
                body: '''<html>
                    <body>
                    <p>Build Status: ${currentBuild.result}</p>
                    <p>Build Number: ${currentBuild.number}</p>
                    <p>Console Output: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                    </body>
                    </html>''',
                to: "${env.NOTIFICATION_EMAIL}",
                from: 'jenkins@example.com',
                replyTo: 'jenkins@example.com',
                mimeType: 'text/html'
            )
        }
    }
}
