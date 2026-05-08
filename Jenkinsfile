pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl
                IF EXIST JMeter_Report.zip del /Q JMeter_Report.zip
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl
                '''
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat '''
                IF EXIST report rmdir /S /Q report
                jmeter -g results.jtl -o report
                dir report
                '''
            }
        }

        stage('Zip Report') {
            steps {
                bat '''
                powershell Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force
                '''
            }
        }
    }

    post {

        success {
            script {
                if (fileExists('JMeter_Report.zip')) {
                    emailext (
                        to: 'bavishasundar@gmail.com',
                        subject: "📊 JMeter Report Ready - Build #${env.BUILD_NUMBER}",
                        body: """
Hi,

Your JMeter report has been generated successfully.

✔ Build: ${env.BUILD_NUMBER}
✔ Job: ${env.JOB_NAME}

📁 Report is attached.

Regards,
Jenkins
""",
                        attachmentsPattern: 'JMeter_Report.zip'
                    )
                } else {
                    echo "Report ZIP not found, email skipped"
                }
            }
        }

        failure {
            emailext (
                to: 'bavishasundar@gmail.com',
                subject: "❌ JMeter FAILED - Build #${env.BUILD_NUMBER}",
                body: "Build failed. Check Jenkins logs."
            )
        }
    }
}
