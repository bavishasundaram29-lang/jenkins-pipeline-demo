post {

    success {
        emailext(
            to: "bavishasundaram29@gmail.com",
            subject: "✅ JMeter Report SUCCESS - Build ${env.BUILD_NUMBER}",
            body: """
Hello,

Your JMeter test has completed successfully.

✔ Build Number: ${env.BUILD_NUMBER}
✔ Status: SUCCESS

📊 Report is available in Jenkins:
- Open build → JMeter HTML Report tab

Regards,
Jenkins
            """,
            attachmentsPattern: 'results.jtl,jmeter.log'
        )
    }

    failure {
        emailext(
            to: "bavishasundaram29@gmail.com",
            subject: "❌ JMeter Test FAILED - Build ${env.BUILD_NUMBER}",
            body: """
Hello,

Your JMeter test has FAILED.

✔ Build Number: ${env.BUILD_NUMBER}

Check Jenkins logs for details.

Regards,
Jenkins
            """
        )
    }
}
