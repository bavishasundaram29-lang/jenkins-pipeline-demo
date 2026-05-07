pipeline {
    agent any

    environment {
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
    }

    stages {

        stage('Run JMeter Test') {
            steps {
                bat """
                jmeter -n -t test.jmx -l %RESULTS% -j %LOG_FILE% -e -o %REPORT_DIR%
                """
            }
        }

        stage('Verify Outputs') {
            steps {
                bat """
                echo ===== LOG FILE =====
                type %LOG_FILE%

                echo ===== JTL SAMPLE =====
                type %RESULTS%
                """
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: '**/*.jtl, **/report/**, **/*.log', fingerprint: true
            }
        }
    }

    post {
        always {
            emailext (
                subject: "JMeter Report - Build ${BUILD_NUMBER}",
                body: "Test execution completed. Please find attached reports.",
                to: "yourmail@gmail.com",
                attachmentsPattern: "results.jtl, jmeter.log, report/index.html"
            )
        }
    }
}
