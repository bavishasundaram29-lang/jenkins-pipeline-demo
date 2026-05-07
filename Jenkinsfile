pipeline {
    agent any

    environment {
        JMX_FILE = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
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
                if exist %RESULTS% del /f /q %RESULTS%
                if exist %LOG_FILE% del /f /q %LOG_FILE%
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter test...

                jmeter -n -t %JMX_FILE% ^
                -l %RESULTS% ^
                -j %LOG_FILE%

                jmeter -g %RESULTS% -o %REPORT_DIR%

                echo Test Completed
                """
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: '**/*.jtl, **/report/**, **/*.log', fingerprint: true
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML([
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true
                ])
            }
        }
    }

    post {

        always {
            emailext (
                to: "bavishasundaram29@gmail.com",
                subject: "JMeter Report - Build ${BUILD_NUMBER}",
                body: """
                Hi,

                Your JMeter test execution is completed successfully.

                Build Number: ${BUILD_NUMBER}
                Status: ${currentBuild.currentResult}

                👉 View Report in Jenkins:
                ${BUILD_URL}JMeter_HTML_Report/

                Thanks
                """
            )
        }
    }
}
