pipeline {
    agent any

    environment {
        JMX_FILE = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: "https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git", branch: "main"
            }
        }

        stage('Clean Workspace') {
            steps {
                bat """
                echo Cleaning workspace...

                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%
                if exist %RESULTS% del /f /q %RESULTS%
                if exist %LOG_FILE% del /f /q %LOG_FILE%

                echo Cleanup completed
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter Test...

                jmeter -n -t %JMX_FILE% ^
                -l %RESULTS% ^
                -j %LOG_FILE%

                echo JMeter execution completed
                """
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat """
                echo Generating HTML Report...

                if not exist %RESULTS% (
                    echo ERROR: results.jtl not found
                    exit /b 1
                )

                jmeter -g %RESULTS% -o %REPORT_DIR%

                echo ===== REPORT GENERATED =====
                dir %REPORT_DIR%
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
            echo "Sending email notification..."

            emailext (
                to: "bavishasundaram29@gmail.com",
                subject: "JMeter Report - Build ${BUILD_NUMBER}",
                body: """
                Hi,

                Your JMeter test execution is completed.

                ✔ Build Number: ${BUILD_NUMBER}
                ✔ Status: ${currentBuild.currentResult}

                👉 Download Report:
                ${BUILD_URL}artifact/report/index.html

                Thanks
                """
            )
        }
    }
}
