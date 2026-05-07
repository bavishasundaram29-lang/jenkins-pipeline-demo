pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
        TEST_PLAN = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS_FILE = "results.jtl"
        REPORT_DIR = "report"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Using SCM checkout (already done by Jenkins)"
            }
        }

        stage('Clean Workspace') {
            steps {
                echo "Cleaning old results and report..."

                bat '''
                    if exist results.jtl del /f /q results.jtl
                    if exist report rmdir /s /q report
                    mkdir report
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                echo "Executing JMeter test..."

                bat """
                    ${JMETER_HOME}\\bin\\jmeter.bat -n ^
                    -t ${TEST_PLAN} ^
                    -l ${RESULTS_FILE} ^
                    -e -o ${REPORT_DIR}
                """
            }
        }

        stage('Verify Report Generation') {
            steps {
                echo "Verifying output files..."

                bat '''
                    echo Checking JTL file...
                    dir results.jtl

                    echo Checking HTML report folder...
                    dir report
                '''
            }
        }
    }

    post {

        always {
            echo "Archiving test results..."

            archiveArtifacts artifacts: 'results.jtl, report/**', fingerprint: true
        }

        success {
            echo "Build SUCCESS - Sending email with report"

            emailext (
                to: "your_email@gmail.com",
                subject: "✅ JMeter Report Success - Build #${BUILD_NUMBER}",
                body: """
                    <h2>JMeter Performance Test Completed Successfully</h2>
                    <p><b>Build Number:</b> ${BUILD_NUMBER}</p>
                    <p><b>Status:</b> SUCCESS</p>
                    <p>Attached is the HTML report.</p>
                """,
                attachmentsPattern: "report/**"
            )
        }

        failure {
            echo "Build FAILED - Sending failure email"

            emailext (
                to: "your_email@gmail.com",
                subject: "❌ JMeter Build Failed - #${BUILD_NUMBER}",
                body: """
                    <h2>JMeter Test Failed</h2>
                    <p><b>Build Number:</b> ${BUILD_NUMBER}</p>
                    <p>Please check Jenkins console logs.</p>
                """
            )
        }
    }
}
