stage('Send Final Report Email') {
    steps {
        script {

            def buildStatus = currentBuild.currentResult
            def reportUrl = "${env.BUILD_URL}JMeter_20HTML_20Report/"

            def emailSubject = ""
            def emailBody = ""

            if (buildStatus == "SUCCESS") {

                emailSubject = "✅ SUCCESS: JMeter Report - Build ${env.BUILD_NUMBER}"

                emailBody = """
                <html>
                <body>
                <h2 style="color:green;">JMeter Test Execution Successful</h2>

                <p>Hello,</p>

                <p>Your JMeter performance test has been completed successfully.</p>

                <table border="1" cellpadding="5" cellspacing="0">
                    <tr>
                        <td><b>Build Number</b></td>
                        <td>${env.BUILD_NUMBER}</td>
                    </tr>
                    <tr>
                        <td><b>Job Name</b></td>
                        <td>${env.JOB_NAME}</td>
                    </tr>
                    <tr>
                        <td><b>Status</b></td>
                        <td style="color:green;"><b>SUCCESS</b></td>
                    </tr>
                </table>

                <br/>

                <p><b>📊 Jenkins HTML Report:</b></p>
                <a href="${reportUrl}">Click here to view JMeter Report</a>

                <br/><br/>
                <p><b>Build Details:</b></p>
                <a href="${env.BUILD_URL}">Open Jenkins Build</a>

                <br/><br/>
                <p>Regards,<br/>Jenkins CI/CD</p>
                </body>
                </html>
                """

            } else {

                emailSubject = "❌ FAILURE: JMeter Report - Build ${env.BUILD_NUMBER}"

                emailBody = """
                <html>
                <body>
                <h2 style="color:red;">JMeter Test Failed</h2>

                <p>Hello,</p>

                <p>The JMeter pipeline has failed. Please check logs.</p>

                <p><b>Jenkins Build:</b></p>
                <a href="${env.BUILD_URL}">Open Build</a>

                <br/><br/>
                <p>Regards,<br/>Jenkins CI/CD</p>
                </body>
                </html>
                """
            }

            emailext(
                to: 'bavishasundar@gmail.com',
                subject: emailSubject,
                mimeType: 'text/html',
                body: emailBody
            )
        }
    }
}
