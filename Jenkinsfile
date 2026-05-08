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
                echo Cleaning old data...
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

                IF NOT EXIST results.jtl (
                    echo results.jtl missing
                    exit /b 1
                )

                jmeter -g results.jtl -o report

                echo Report generated successfully
                dir report
                '''
            }
        }

        stage('Zip Report') {
            steps {
                bat '''
                echo Creating ZIP file for report...
                powershell Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force
                dir
                '''
            }
        }
    }

    post {

        success {
            mail to: 'bavishasundar@gmail.com',
                 subject: "✅ JMeter Report - Build #${env.BUILD_NUMBER}",
                 body: """
Hi,

Your JMeter test has completed successfully.

Please find the attached HTML report (zipped).

Build Number: ${env.BUILD_NUMBER}
Jenkins Job: ${env.JOB_NAME}

Regards,
Jenkins
""",
                 attachmentsPattern: 'JMeter_Report.zip'
        }

        failure {
            mail to: 'bavishasundar@gmail.com',
                 subject: "❌ JMeter FAILED - Build #${env.BUILD_NUMBER}",
                 body: """
Hi,

Your JMeter test has FAILED.

Please check Jenkins console logs.

Build Number: ${env.BUILD_NUMBER}
Job: ${env.JOB_NAME}

Regards,
Jenkins
"""
        }
    }
}
