pipeline {
    agent any

    stages {

        stage('Checkout') {
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
                C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat ^
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
                    alwaysLinkToLastBuild: true
                ])
            }
        }
    }

    post {

        success {

            emailext(
                subject: "JMeter Test Report - SUCCESS - Build #${BUILD_NUMBER}",

                body: """
                <h2>JMeter Test Execution Successful</h2>

                <p><b>Job Name:</b> ${JOB_NAME}</p>
                <p><b>Build Number:</b> ${BUILD_NUMBER}</p>

                <p>
                Open Report:
                <a href="${BUILD_URL}JMeter_Report/">
                Click Here
                </a>
                </p>

                <p>Regards,<br>Jenkins</p>
                """,

                mimeType: 'text/html',

                to: 'bavishasundaram29@gmail.com'
            )
        }

        failure {

            emailext(
                subject: "JMeter Test FAILED - Build #${BUILD_NUMBER}",

                body: """
                <h2>Build Failed</h2>

                <p>Check Jenkins console output.</p>

                <p>
                Build URL:
                <a href="${BUILD_URL}">
                Open Build
                </a>
                </p>
                """,

                mimeType: 'text/html',

                to: 'bavishasundaram29@gmail.com'
            )
        }
    }
}
