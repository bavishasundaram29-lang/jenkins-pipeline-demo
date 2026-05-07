pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin"
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
                %JMETER_HOME%\\jmeter.bat ^
                -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e ^
                -o report
                """
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
                <h2 style="color:green;">JMeter Test Passed</h2>

                <p><b>Job:</b> ${JOB_NAME}</p>
                <p><b>Build Number:</b> ${BUILD_NUMBER}</p>

                <p>
                📊 <a href="${BUILD_URL}JMeter_Report/">Open Jenkins Report</a>
                </p>

                <p>Regards,<br>Jenkins</p>
                """,
                mimeType: 'text/html'
            )
        }

        failure {
            emailext(
                to: "${EMAIL_TO}",
                subject: "❌ FAILED - JMeter Build #${BUILD_NUMBER}",
                body: """
                <h2 style="color:red;">JMeter Test Failed</h2>

                <p><b>Job:</b> ${JOB_NAME}</p>
                <p><b>Build Number:</b> ${BUILD_NUMBER}</p>

                <p>
                🔍 <a href="${BUILD_URL}console">View Console Logs</a>
                </p>
                """,
                mimeType: 'text/html'
            )
        }
    }
}
