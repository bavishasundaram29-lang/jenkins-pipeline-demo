stage('Send Final Report Email') {

    script {

        def buildStatus = currentBuild.currentResult
        def reportUrl = "${env.BUILD_URL}JMeter_20HTML_20Report/"

        def emailSubject = ""
        def emailBody = ""

        if (buildStatus == "SUCCESS") {

            emailSubject = "✅ SUCCESS: JMeter Report - Build ${env.BUILD_NUMBER}"

            emailBody = """
            <h2 style="color:green;">JMeter Execution Successful</h2>

            <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
            <p><b>Job:</b> ${env.JOB_NAME}</p>

            <p><b>📊 Report URL:</b></p>
            <a href="${reportUrl}">Open JMeter HTML Report</a>

            <br/><br/>
            <p><b>🔗 Jenkins Build:</b></p>
            <a href="${env.BUILD_URL}">Open Build</a>
            """

        } else {

            emailSubject = "❌ FAILURE: JMeter Report - Build ${env.BUILD_NUMBER}"

            emailBody = """
            <h2 style="color:red;">JMeter Execution Failed</h2>

            <p><b>Build:</b> ${env.BUILD_NUMBER}</p>

            <p><b>🔗 Build URL:</b></p>
            <a href="${env.BUILD_URL}">Open Build</a>
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
