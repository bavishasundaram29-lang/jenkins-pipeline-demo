pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        ZIP_FILE = "report.zip"
        EMAIL_TO = "bavishasundaram29@gmail.com"
    }

    stages {

        stage('Checkout') {
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

        stage('Run JMeter') {
            steps {
                bat '''
                C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e -o report
                '''
            }
        }

        stage('Zip Report') {
            steps {
                powershell '''
                Compress-Archive -Path report -DestinationPath report.zip -Force
                '''
            }
        }

        stage('Verify Files') {
            steps {
                bat 'dir report.zip'
            }
        }
    }

    post {
        always {
            emailext(
                to: "${EMAIL_TO}",
                subject: "Jenkins Build #${BUILD_NUMBER} - ${currentBuild.currentResult}",
                body: """
Build Status: ${currentBuild.currentResult}

Build URL: ${BUILD_URL}

Report is attached in ZIP.
                """,
                attachmentsPattern: "report.zip",
                attachLog: true
            )
        }
    }
}
