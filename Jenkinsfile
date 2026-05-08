pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        JTL_FILE = "results.jtl"
        ZIP_FILE = "JMeter_Report.zip"
        EMAIL_TO = "bavishasundar@gmail.com"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                echo Cleaning workspace...

                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl
                IF EXIST JMeter_Report.zip del /Q JMeter_Report.zip

                echo Cleanup completed
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo Running JMeter Test...

                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl
                '''
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat '''
                echo Generating HTML Report...

                rmdir /S /Q report
                timeout /t 2 > nul

                jmeter -g results.jtl -o report

                echo Validating report...
                dir report

                IF NOT EXIST report\\index.html (
                    echo ERROR: HTML report generation failed
                    exit /b 1
                )
                '''
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

        stage('Zip Report') {
            steps {
                bat '''
                echo Creating ZIP file...

                powershell -Command "Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force"

                dir
                '''
            }
        }
    }

    post {

        success {
            script {
                echo "Checking ZIP before sending email..."

                if (fileExists('JMeter_Report.zip')) {

                    emailext (
                        to: "${EMAIL_TO}",
                        subject: "📊 JMeter Report SUCCESS - Build #${env.BUILD_NUMBER}",
                        body: """
Hi,

Your JMeter performance test completed successfully.

✔ Job: ${env.JOB_NAME}
✔ Build Number: ${env.BUILD_NUMBER}

📊 Jenkins Report:
${env.BUILD_URL}JMeter_20HTML_20Report/

Regards,
Jenkins
""",
                        attachmentsPattern: 'JMeter_Report.zip',
                        mimeType: 'text/plain'
                    )

                } else {
                    echo "ZIP file not found. Email not sent."
                }
            }
        }

        failure {
            emailext (
                to: "${EMAIL_TO}",
                subject: "❌ JMeter FAILED - Build #${env.BUILD_NUMBER}",
                body: """
Hi,

Your JMeter test FAILED.

✔ Job: ${env.JOB_NAME}
✔ Build: ${env.BUILD_NUMBER}

Please check Jenkins logs.

Regards,
Jenkins
"""
            )
        }
    }
}
