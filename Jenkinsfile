pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Clean Workspace') {
            steps {
                bat '''
                    echo Cleaning workspace...
                    IF EXIST report rmdir /S /Q report
                    IF EXIST results.jtl del /Q results.jtl
                    IF EXIST JMeter_Report.zip del /Q JMeter_Report.zip
                    echo Cleanup done
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
                    echo Generating HTML Report...
                    jmeter -g results.jtl -o report
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
                    reportName: 'JMeter_HTML_Report'
                ])
            }
        }

        stage('Create ZIP Report') {
            steps {
                bat '''
                    echo Creating ZIP...
                    powershell -Command "Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force"
                '''
            }
        }

        stage('Send Email with ZIP Report') {
            steps {
                script {

                    emailext(
                        to: 'bavishasundar@gmail.com',
                        subject: "JMeter Report - Build ${env.BUILD_NUMBER}",
                        mimeType: 'text/html',
                        body: """
                            <h2>JMeter Execution Completed</h2>

                            <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                            <p><b>Status:</b> ${currentBuild.currentResult}</p>

                            <p>The full report is attached as ZIP.</p>
                        """,

                        // 🔥 IMPORTANT: ZIP attachment
                        attachmentsPattern: 'JMeter_Report.zip, results.jtl'
                    )
                }
            }
        }
    }
}
