pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                echo Cleaning workspace...

                if exist report rmdir /s /q report
                if exist results.jtl del /q results.jtl
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

        stage('Verify Report') {
            steps {
                bat '''
                echo Checking generated report...

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

        stage('Send Report to Email') {
            steps {
                emailext(
                    to: 'bavishasundar@gmail.com',

                    subject: "JMeter Report - Build ${env.BUILD_NUMBER}",

                    mimeType: 'text/html',

                    body: """
                    <html>
                    <body>

                    <h2 style="color:green;">
                    JMeter Execution Completed Successfully
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
                    <td><b>Build Status</b></td>
                    <td>SUCCESS</td>
                    </tr>

                    </table>

                    <br>

                    <p>
                    Jenkins UI Report:
                    <a href="${env.BUILD_URL}JMeter_20HTML_20Report/">
                    Open JMeter HTML Report
                    </a>
                    </p>

                    <br>

                    <p>
                    JMeter HTML report attached with this email.
                    </p>

                    <br>

                    <p>
                    Regards,<br>
                    Jenkins CI/CD
                    </p>

                    </body>
                    </html>
                    """,

                    attachmentsPattern: 'report/index.html',

                    attachLog: true
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
