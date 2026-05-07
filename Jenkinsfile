pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
        TEST_PLAN = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
    }

    stages {

        stage('Clean Old Reports') {
            steps {
                echo "Deleting old report and results..."

                bat """
                if exist ${REPORT_DIR} rmdir /s /q ${REPORT_DIR}
                if exist ${RESULTS} del /f /q ${RESULTS}
                """
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
                bat "if not exist ${REPORT_DIR}\\index.html exit 1"
            }
        }
    }

    post {

        success {
            emailext (
                subject: "✔ JMeter Report - Build #${env.BUILD_NUMBER}",
                body: "Test completed successfully. Report attached.",
                attachmentsPattern: "${REPORT_DIR}/**/*"
            )
        }

        failure {
            emailext (
                subject: "❌ JMeter Failed - Build #${env.BUILD_NUMBER}",
                body: "Check Jenkins logs for details."
            )
        }

        always {
            echo "Pipeline finished"
        }
    }
}
