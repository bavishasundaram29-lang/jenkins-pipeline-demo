pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git'
            }
        }

        stage('Clean Workspace') {
            steps {
                bat '''
                if exist report rmdir /s /q report
                if exist report.zip del /f /q report.zip
                if exist results.jtl del /f /q results.jtl
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                %JMETER_HOME%\\bin\\jmeter.bat -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e -o report
                """
            }
        }

        stage('Publish HTML Report') {
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
                Compress-Archive -Path report\\* -DestinationPath report.zip -Force
                '''
            }
        }
    }

    post {
        always {

            emailext(
                subject: "JMeter Test Report - Build #${BUILD_NUMBER}",
                body: """
                Build Completed Successfully.

                Job Name: ${JOB_NAME}
                Build Number: ${BUILD_NUMBER}

                JMeter HTML Report attached.

                Jenkins URL:
                ${BUILD_URL}
                """,

                to: 'bavishasundaram29@gmail.com',

                attachmentsPattern: 'report.zip',

                mimeType: 'text/plain'
            )
        }
    }
}
