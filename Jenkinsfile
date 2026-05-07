pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
        TEST_PLAN = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Code already checked out by Jenkins SCM"
            }
        }

        stage('Run JMeter Test') {
            steps {
                echo "Running JMeter performance test..."

                bat """
                ${JMETER_HOME}\\bin\\jmeter.bat -n ^
                -t ${TEST_PLAN} ^
                -l ${RESULTS} ^
                -e -o ${REPORT_DIR}
                """
            }
        }

        stage('Verify Report') {
            steps {
                echo "Verifying HTML report..."

                bat """
                if not exist ${REPORT_DIR}\\index.html (
                    echo Report not generated
                    exit 1
                )
                """
            }
        }
    }

    post {

        success {
            echo "Build Successful - Sending Email"

            emailext (
                subject: "✔ JMeter Report Generated - Build #${env.BUILD_NUMBER}",
                body: """
                Hi Team,

                JMeter test executed successfully.

                📌 Build Number: ${env.BUILD_NUMBER}
                📂 Workspace: ${env.WORKSPACE}
                📊 Report Path: ${env.WORKSPACE}\\${REPORT_DIR}\\index.html

                Please find attached full HTML report.

                Regards,
                Jenkins Pipeline
                """,
                attachmentsPattern: "${REPORT_DIR}/**/*",
                mimeType: 'text/html'
            )
        }

        failure {
            echo "Build Failed - Sending Email"

            emailext (
                subject: "❌ JMeter Test Failed - Build #${env.BUILD_NUMBER}",
                body: """
                Hi Team,

                JMeter test execution failed.

                Please check Jenkins logs for details.

                Regards,
                Jenkins Pipeline
                """
            )
        }

        always {
            echo "Pipeline execution completed"
        }
    }
}
