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

                jmeter -n ^
                -t jpetstore_jenkins/SCR01_Jpetstore.jmx ^
                -l results.jtl
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

        stage('Publish HTML Report in Jenkins UI') {
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

        stage('Create ZIP Report') {
            steps {
                powershell '''
                Compress-Archive -Path report\\* -DestinationPath JMeter_Report.zip -Force
                '''
            }
        }

        stage('Send Email with ZIP Report') {
            steps {
                emailext(
                    to: 'bavishasundar@gmail.com',

                    subject: "JMeter Report - Build ${env.BUILD_NUMBER}",

                    mimeType: 'text/html',

                    body: """
                    <html>
                    <body>

                    <h2 style="color:green;">
                    JMeter Test Execution Completed Successfully
                    </h2>

                    <table border="1" cellpadding="5" cellspacing="0">

                        <tr>
                            <td><b>Job Name</b></td>
                            <td>${env.JOB_NAME}</td>
                        </tr>

                        <tr>
                            <td><b>Build Number</b></td>
                            <td>${env.BUILD_NUMBER}</td>
                        </tr>

                        <tr>
                            <td><b>Status</b></td>
                            <td>SUCCESS</td>
                        </tr>

                    </table>

                    <br>

                    <p>
                    Jenkins HTML Report:
                    </p>

                    <a href="${env.BUILD_URL}JMeter_20HTML_20Report/">
                    Open Jenkins Report
                    </a>

                    <br><br>

                    <p>
                    JMeter ZIP report is attached with this email.
                    </p>

                    <br>

                    <p>
                    Regards,<br>
                    Jenkins CI/CD
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
            echo 'Pipeline completed'
        }
    }
}
