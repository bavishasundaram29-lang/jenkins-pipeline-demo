pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        JTL_FILE = "results.jtl"
        EMAIL_TO = "bavishasundar@gmail.com"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                echo Cleaning workspace...

                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl

                echo Clean completed
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

                echo Report generated
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
    }

    post {

        success {
            script {

                // Jenkins UI report URL
                def reportUrl = "${env.BUILD_URL}JMeter_20HTML_20Report/"

                emailext (
                    to: "${EMAIL_TO}",
                    subject: "✅ JMeter Report SUCCESS - Build #${env.BUILD_NUMBER}",
                    body: """
Hi,

Your JMeter test completed successfully.

✔ Job Name: ${env.JOB_NAME}
✔ Build Number: ${env.BUILD_NUMBER}

📊 Jenkins HTML Report:
${reportUrl}

You can open the report directly from Jenkins.

Regards,
Jenkins
""",
                    mimeType: 'text/plain'
                )
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

Check Jenkins logs for details.

Regards,
Jenkins
""",
                mimeType: 'text/plain'
            )
        }
    }
}
