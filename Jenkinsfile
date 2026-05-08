pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        REPORT_ZIP = "JMeter_Report.zip"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                echo Cleaning old files...

                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl
                IF EXIST JMeter_Report.zip del /Q JMeter_Report.zip
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

                IF EXIST report rmdir /S /Q report

                jmeter -g results.jtl -o report

                echo Report generated:
                dir report
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
                echo Zipping report...

                powershell Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force
                '''
            }
        }
    }

    post {

        success {
            emailext (
                to: 'bavishasundar@gmail.com',
                subject: "✅ JMeter Report SUCCESS - Build #${env.BUILD_NUMBER}",
                body: """
Hi,

Your JMeter test completed successfully.

✔ Jenkins Build: ${env.BUILD_NUMBER}
✔ Job: ${env.JOB_NAME}

📊 Report is available in Jenkins UI
📎 ZIP report is attached

Regards,
Jenkins
""",
                attachmentsPattern: 'JMeter_Report.zip'
            )
        }

        failure {
            emailext (
                to: 'bavishasundar@gmail.com',
                subject: "❌ JMeter FAILED - Build #${env.BUILD_NUMBER}",
                body: """
Hi,

Your JMeter test FAILED.

Check Jenkins console logs.

Build: ${env.BUILD_NUMBER}
Job: ${env.JOB_NAME}
"""
            )
        }
    }
}
