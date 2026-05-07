pipeline {
    agent any

    environment {
        TEST_PLAN = "test.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Cloning your GitHub repository..."
                git url: "https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git"
            }
        }

        stage('Clean Workspace') {
            steps {
                bat """
                echo Cleaning old files...

                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%
                if exist %RESULTS% del /f /q %RESULTS%
                if exist %LOG_FILE% del /f /q %LOG_FILE%

                echo Cleanup done
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter test...

                jmeter -n -t %TEST_PLAN% ^
                -l %RESULTS% ^
                -j %LOG_FILE% ^
                -e -o %REPORT_DIR%

                echo Test completed
                """
            }
        }

        stage('Verify Outputs') {
            steps {
                bat """
                echo ===== LOG FILE =====
                type %LOG_FILE%

                echo ===== RESULTS SAMPLE =====
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
            echo "Sending email to you..."

            emailext (
                subject: "JMeter Report - Build ${BUILD_NUMBER}",
                body: """
                Hi,

                Your JMeter execution is completed.

                Build Number: ${BUILD_NUMBER}
                Status: ${currentBuild.currentResult}

                Please check attached reports.

                Thanks
                """,
                to: "bavishasundaram29@gmail.com",
                attachmentsPattern: "results.jtl, jmeter.log, report/index.html"
            )
        }
    }
}
