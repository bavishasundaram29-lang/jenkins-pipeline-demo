pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
        REPORT_DIR = "report"
        JTL_FILE = "results.jtl"
        ZIP_FILE = "JMeter_Report.zip"
        EMAIL_TO = "bavishasundar@gmail.com"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                bat """
                echo Cleaning workspace...
                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%
                if exist %JTL_FILE% del /q %JTL_FILE%
                if exist %ZIP_FILE% del /q %ZIP_FILE%
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter Test...
                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l %JTL_FILE%
                """
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat """
                echo Generating HTML Report...
                jmeter -g %JTL_FILE% -o %REPORT_DIR%
                """
            }
        }

        stage('Publish HTML Report in Jenkins UI') {
            steps {
                publishHTML([
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }

        stage('Zip Report') {
            steps {
                powershell """
                Compress-Archive -Path ${env.REPORT_DIR}\\* -DestinationPath ${env.ZIP_FILE} -Force
                """
            }
        }

        stage('Send Email with ZIP Report') {
            steps {
                script {
                    def status = currentBuild.currentResult

                    def subject = "JMeter Report - Build ${env.BUILD_NUMBER} - ${status}"

                    def body = """
                    Hello,

                    Please find attached JMeter performance report ZIP.

                    Build: ${env.BUILD_NUMBER}
                    Job: ${env.JOB_NAME}
                    Status: ${status}

                    Thanks
                    Jenkins CI
                    """

                    emailext(
                        to: "${env.EMAIL_TO}",
                        subject: subject,
                        body: body,
                        attachmentsPattern: "JMeter_Report.zip",
                        mimeType: "text/plain"
                    )
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed"
        }
    }
}
