pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
        REPORT_DIR  = "report"
        JTL_FILE    = "results.jtl"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Clean Workspace') {
            steps {
                bat """
                echo Cleaning workspace...
                IF EXIST %REPORT_DIR% rmdir /S /Q %REPORT_DIR%
                IF EXIST %JTL_FILE% del /Q %JTL_FILE%
                IF EXIST JMeter_Report.zip del /Q JMeter_Report.zip
                echo Cleanup completed
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter Test...
                %JMETER_HOME%\\bin\\jmeter.bat -n -t jpetstore_jenkins\\SCR01_Jpetstore.jmx -l %JTL_FILE%
                """
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat """
                echo Generating HTML Report...

                IF EXIST %REPORT_DIR% rmdir /S /Q %REPORT_DIR%

                %JMETER_HOME%\\bin\\jmeter.bat -g %JTL_FILE% -o %REPORT_DIR%
                """
            }
        }

        stage('Publish HTML Report in Jenkins UI') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter_HTML_Report'
                ])
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'report/**', fingerprint: true
            }
        }
    }

    post {

        success {
            script {
                // Jenkins UI report URL
                def reportUrl = "${env.BUILD_URL}JMeter_HTML_Report/"

                echo "Report URL: ${reportUrl}"

                emailext(
                    to: "bavishasundar@gmail.com",
                    subject: "JMeter Test Report - Build #${env.BUILD_NUMBER}",
                    body: """
Hi,

Your JMeter test has been completed successfully.

 Jenkins Report URL:
${reportUrl}

 Build Details:
- Job: ${env.JOB_NAME}
- Build Number: ${env.BUILD_NUMBER}
- Status: SUCCESS

Thanks,
Jenkins CI
"""
                )
            }
        }

        failure {
            emailext(
                to: "bavishasundar@gmail.com",
                subject: "JMeter Test FAILED - Build #${env.BUILD_NUMBER}",
                body: "Build failed. Please check Jenkins logs."
            )
        }
    }
}
