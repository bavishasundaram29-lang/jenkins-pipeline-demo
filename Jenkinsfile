pipeline {
    agent any

    environment {
        JMETER_PATH = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat"
        EMAIL_TO = "bavishasundaram29@gmail.com"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Clean Old Files') {
            steps {
                bat '''
                if exist report rmdir /s /q report
                if exist results.jtl del /f /q results.jtl
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                "%JMETER_PATH%" ^
                -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e ^
                -o report
                """
            }
        }

        stage('Verify Report') {
            steps {
                bat 'dir report'
            }
        }

        stage('Publish Report') {
            steps {
                publishHTML(target: [
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }

    post {

        success {
            emailext(
                to: "${EMAIL_TO}",
                subject: "✅ SUCCESS - JMeter Build #${BUILD_NUMBER}",
                body: """
                <h2>JMeter Test Passed</h2>

                <p><b>Job:</b> ${JOB_NAME}</p>
                <p><b>Build:</b> ${BUILD_NUMBER}</p>

                <p>
                📊 <a href="${BUILD_URL}JMeter_Report/">Open Report</a>
                </p>
                """,
                mimeType: 'text/html'
            )
        }

        failure {
            emailext(
                to: "${EMAIL_TO}",
                subject: "❌ FAILED - JMeter Build #${BUILD_NUMBER}",
                body: """
                <h2>JMeter Test Failed</h2>

                <p>Check console logs</p>

                <p><a href="${BUILD_URL}console">Open Logs</a></p>
                """,
                mimeType: 'text/html'
            )
        }
    }
}
