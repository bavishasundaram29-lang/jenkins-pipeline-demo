pipeline {
    agent any

    environment {
        JMX_FILE = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS_FILE = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
        EMAIL_TO = "bavishasundaram29@gmail.com"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: "https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git", branch: "main"
            }
        }

        stage('Clean Workspace') {
            steps {
                bat """
                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%
                if exist %RESULTS_FILE% del /f /q %RESULTS_FILE%
                if exist %LOG_FILE% del /f /q %LOG_FILE%
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                jmeter -n -t %JMX_FILE% ^
                -l %RESULTS_FILE% ^
                -j %LOG_FILE%
                """
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat """
                jmeter -g %RESULTS_FILE% -o %REPORT_DIR%
                """
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

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/report/**, results.jtl, jmeter.log'
            }
        }
    }

    post {

        success {
            emailext(
                to: "${EMAIL_TO}",
                subject: "✅ JMeter Test Passed - Build ${env.BUILD_NUMBER}",
                body: """
Hello,

Your JMeter test has completed successfully.

✔ Build Number: ${env.BUILD_NUMBER}
✔ Status: SUCCESS
✔ Report: Available in Jenkins UI (JMeter HTML Report tab)

Regards,
Jenkins CI
                """,
                attachmentsPattern: 'results.jtl,jmeter.log'
            )
        }

        failure {
            emailext(
                to: "${EMAIL_TO}",
                subject: "❌ JMeter Test Failed - Build ${env.BUILD_NUMBER}",
                body: """
Hello,

Your JMeter test has FAILED.

✔ Build Number: ${env.BUILD_NUMBER}
❌ Please check Jenkins logs.

Regards,
Jenkins CI
                """
            )
        }
    }
}
