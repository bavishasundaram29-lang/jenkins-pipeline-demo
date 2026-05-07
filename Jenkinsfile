pipeline {
    agent any

    environment {
        EMAIL_TO = "bavishasundaram29@gmail.com"
    }


    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git',
                    branch: 'main'
            }
        }

        stage('Clean Workspace') {
            steps {
                bat '''
                    if exist report rmdir /s /q report
                    if exist results.jtl del /f /q results.jtl
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e -o report
                '''
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

        stage('Verify Report') {
            steps {
                bat '''
                echo Report Files:
                dir report
                '''
            }
        }
    }

    post {

        success {

            emailext(
                to: "${EMAIL_TO}",

                subject: "JMeter Build Success - ${BUILD_NUMBER}",

                body: """
Build completed successfully.

JMeter HTML Report:
${BUILD_URL}JMeter_20HTML_20Report/

Build URL:
${BUILD_URL}
                """,

                attachLog: true
            )
        }

        failure {

            emailext(
                to: "${EMAIL_TO}",

                subject: "JMeter Build Failed - ${BUILD_NUMBER}",

                body: """
Build failed.

Check Jenkins:
${BUILD_URL}
                """,

                attachLog: true
            )
        }
    }
}
