pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
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
                \"${JMETER_HOME}\\bin\\jmeter.bat\" -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e -o report
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
                publishHTML([
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter_Report',
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
                subject: "$PROJECT_NAME - Build # $BUILD_NUMBER - SUCCESS",
                body: """
                <h2>JMeter Test Execution Successful</h2>

                <p><b>Project:</b> $PROJECT_NAME</p>
                <p><b>Build Number:</b> $BUILD_NUMBER</p>
                <p><b>Status:</b> SUCCESS</p>

                <p>View Console Output:<br>
                <a href="$BUILD_URL/console">$BUILD_URL/console</a></p>

                <p>View JMeter Report:<br>
                <a href="$BUILD_URL/JMeter_Report">$BUILD_URL/JMeter_Report</a></p>
                """,
                mimeType: 'text/html'
            )
        }

        failure {
            emailext(
                to: "${EMAIL_TO}",
                subject: "$PROJECT_NAME - Build # $BUILD_NUMBER - FAILED",
                body: """
                <h2 style="color:red;">JMeter Test Failed</h2>

                <p><b>Project:</b> $PROJECT_NAME</p>
                <p><b>Build Number:</b> $BUILD_NUMBER</p>
                <p><b>Status:</b> FAILED</p>

                <p>Check logs:<br>
                <a href="$BUILD_URL/console">$BUILD_URL/console</a></p>
                """,
                mimeType: 'text/html'
            )
        }
    }
}
