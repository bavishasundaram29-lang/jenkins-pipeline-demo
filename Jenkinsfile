pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
        TEST_PLAN = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Cloning repository..."
                git branch: 'main',
                    url: 'https://github.com/YOUR_REPO_URL.git'
            }
        }

        stage('Run JMeter Test') {
            steps {
                echo "Executing JMeter Test..."
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
                echo "Checking report generation..."
                bat "if not exist ${REPORT_DIR}\\index.html exit 1"
            }
        }
    }

    post {

        success {
            echo "Build Successful - Sending Email with Report"

            emailext (
                subject: "✔ JMeter Test Completed - Build #${env.BUILD_NUMBER}",
                body: """
                Hi Team,

                JMeter performance test has completed successfully.

                📌 Build Number: ${env.BUILD_NUMBER}
                📊 Report Location: ${env.WORKSPACE}\\${REPORT_DIR}\\index.html

                Please find attached HTML report.

                Regards,
                Jenkins Pipeline
                """,
                attachmentsPattern: "${REPORT_DIR}/**/*",
                mimeType: 'text/html'
            )
        }

        failure {
            echo "Build Failed - Sending Failure Email"

            emailext (
                subject: "❌ JMeter Test Failed - Build #${env.BUILD_NUMBER}",
                body: """
                Hi Team,

                JMeter test execution failed.

                Please check Jenkins logs.

                Regards,
                Jenkins Pipeline
                """
            )
        }

        always {
            echo "Pipeline Execution Completed"
        }
    }
}
