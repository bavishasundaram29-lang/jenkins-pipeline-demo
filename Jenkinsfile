pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        ZIP_FILE = "report.zip"
        RECIPIENT = "bavishasundaram29@gmail.com"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git'
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
                bat """
                C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e -o report
                """
            }
        }

        stage('Zip Report') {
            steps {
                powershell """
                Compress-Archive -Path report -DestinationPath report.zip -Force
                """
            }
        }
    }

    post {

        success {
            emailext(
                to: "${RECIPIENT}",
                subject: "Jenkins Build Success - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Your JMeter test has completed successfully.

Build: ${env.BUILD_URL}

Report is attached in ZIP file.
                """,
                
                attachmentsPattern: "report.zip"
            )
        }

        failure {
            emailext(
                to: "${RECIPIENT}",
                subject: "Jenkins Build FAILED - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Build Failed.

Check logs: ${env.BUILD_URL}
                """,
                attachmentsPattern: "report.zip"
            )
        }
    }
}
