pipeline {
    agent any

    environment {
        JMX_FILE = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS_FILE = "results.jtl"
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
                if exist %RESULTS_FILE% del /f /q %RESULTS_FILE%
                if exist %LOG_FILE% del /f /q %LOG_FILE%

                echo Cleanup completed
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter...

                jmeter -n -t %JMX_FILE% ^
                -l %RESULTS_FILE% ^
                -j %LOG_FILE%
                """
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat """
                echo Generating HTML Report...

                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%

                jmeter -g %RESULTS_FILE% -o %REPORT_DIR%

                echo Report generated successfully
                dir %REPORT_DIR%
                """
            }
        }

        stage('Publish Report in Jenkins UI') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'
                ])
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'results.jtl, jmeter.log, report/**'
            }
        }
    }

    post {

        success {
            emailext(
                to: "bavisahsundar@gmail.com",
                subject: "JMeter Report - SUCCESS Build #${BUILD_NUMBER}",
                body: """
Hi,

Your JMeter Test Execution is SUCCESS.

Build Number: ${BUILD_NUMBER}

Report is attached from Jenkins workspace:
${BUILD_URL}artifact/report/index.html

Thanks,
Jenkins
""",
                attachmentsPattern: "report/**"
            )
        }

        failure {
            emailext(
                to: "bavisahsundar@gmail.com",
                subject: "JMeter Report - FAILED Build #${BUILD_NUMBER}",
                body: """
Hi,

Your JMeter Test Execution FAILED.

Check Jenkins logs:
${BUILD_URL}

Thanks,
Jenkins
"""
            )
        }
    }
}
