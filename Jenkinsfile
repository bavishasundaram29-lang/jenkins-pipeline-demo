pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        ZIP_FILE = "report.zip"
        EMAIL_TO = "bavishasundaram29@gmail.com"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git',
                    branch: 'main'
            }
        }

        stage('Clean Workspace') {
            steps {
                bat '''
                    if exist report rmdir /s /q report
                    if exist report.zip del /f /q report.zip
                    if exist results.jtl del /f /q results.jtl
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e -o report
                '''
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'
                ])
            }
        }

        stage('Create ZIP Report') {
            steps {
                powershell '''
                Compress-Archive -Path report -DestinationPath report.zip -Force
                '''
            }
        }

        stage('Verify ZIP File') {
            steps {
                bat '''
                echo Checking ZIP File...
                dir
                dir report.zip
                '''
            }
        }
    }

    post {

        success {

            emailext(
                to: "${EMAIL_TO}",

                subject: "JMeter Report - Build #${BUILD_NUMBER} - SUCCESS",

                mimeType: 'text/html',

                body: """
                <h2>JMeter Test Execution Completed Successfully</h2>

                <p><b>Build Number:</b> ${BUILD_NUMBER}</p>

                <p><b>Build Status:</b> ${currentBuild.currentResult}</p>

                <p>
                JMeter HTML Report has been generated successfully.
                </p>

                <p>
                ZIP Report is attached with this email.
                </p>

                <p>
                <a href="${BUILD_URL}">
                Open Jenkins Build
                </a>
                </p>
                """,

                attachmentsPattern: '**/report.zip',

                attachLog: true
            )
        }

        failure {

            emailext(
                to: "${EMAIL_TO}",

                subject: "JMeter Report - Build #${BUILD_NUMBER} - FAILED",

                body: """
                JMeter Pipeline Failed.

                Check Jenkins Console Output:
                ${BUILD_URL}
                """,

                attachLog: true
            )
        }
    }
}
