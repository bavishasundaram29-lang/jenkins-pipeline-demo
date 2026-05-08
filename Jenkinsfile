pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                echo Cleaning workspace...

                if exist report rmdir /s /q report
                if exist results.jtl del /q results.jtl
                if exist JMeter_Report.zip del /q JMeter_Report.zip
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
                    reportName: 'JMeter HTML Report'
                ])
            }
        }

        stage('Create ZIP File') {
            steps {
                powershell '''
                Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force
                '''
            }
        }

        stage('Send Report to Email') {
            steps {
                emailext(
                    to: 'bavishasundar@gmail.com',

                    subject: "JMeter Report - Build ${env.BUILD_NUMBER}",

                    mimeType: 'text/html',

                    body: """
                    <html>
                    <body>

                    <h2>JMeter Execution Completed</h2>

                    <p><b>Job Name:</b> ${env.JOB_NAME}</p>

                    <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>

                    <p>
                    Jenkins UI Report:
                    <a href="${env.BUILD_URL}JMeter_20HTML_20Report/">
                    Open Report
                    </a>
                    </p>

                    <p>
                    ZIP report attached with this email.
                    </p>

                    </body>
                    </html>
                    """,

                    attachmentsPattern: 'JMeter_Report.zip'
                )
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed successfully'
        }
    }
}
