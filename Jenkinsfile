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

                echo Report generated successfully
                dir report
                '''
            }
        }

        stage('Send Email After Report') {
            steps {
                script {
                    emailext (
                        to: 'bavishasundar@gmail.com',
                        subject: " JMeter Report Ready - Build #${env.BUILD_NUMBER}",
                        body: """
Hi,

Your JMeter HTML report has been generated successfully.

✔ Build Number: ${env.BUILD_NUMBER}
✔ Job: ${env.JOB_NAME}

 Report is ready in Jenkins workspace
 You can also view it in Jenkins UI (if publishHTML is enabled)

Regards,
Jenkins
"""
                    )
                }
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

                powershell Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force
                '''
            }
        }
    }
}
