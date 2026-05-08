pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                echo Cleaning workspace...

                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl
                IF EXIST JMeter_Report.zip del /Q JMeter_Report.zip
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo Running JMeter...

                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl
                '''
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat '''
                echo Generating report...

                IF EXIST report rmdir /S /Q report

                jmeter -g results.jtl -o report

                echo Checking report folder:
                dir report

                IF NOT EXIST report\\index.html (
                    echo ERROR: HTML report not generated
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
                echo Creating ZIP...

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
                        subject: "📊 JMeter Report SUCCESS - Build #${env.BUILD_NUMBER}",
                        body: """
Hi,

Your JMeter execution completed successfully.

✔ Build: ${env.BUILD_NUMBER}
✔ Job: ${env.JOB_NAME}

📊 Report is available in Jenkins UI
📎 ZIP report attached

Regards,
Jenkins
""",
                        attachmentsPattern: 'JMeter_Report.zip'
                    )
                } else {
                    echo "ZIP not found - email skipped"
                }
            }
        }

        failure {
            emailext (
                to: 'bavishasundar@gmail.com',
                subject: "❌ JMeter FAILED - Build #${env.BUILD_NUMBER}",
                body: "Build failed. Check Jenkins console output."
            )
        }
    }
}
