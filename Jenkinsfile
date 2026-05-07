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
                    if exist report.zip del /f /q report.zip
                    if exist results.jtl del /f /q results.jtl
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                "C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -e -o report
                """
            }
        }

        stage('Verify Report') {
            steps {
                bat 'dir report'
            }
        }

        stage('Zip Report') {
            steps {
                bat '''
                    powershell -Command "Compress-Archive -Path report -DestinationPath report.zip -Force"
                '''
            }
        }
    }

    post {
        always {
            emailext(
                to: "${env.EMAIL_TO}",
                subject: "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - ${currentBuild.currentResult}",
                body: """
Build Status: ${currentBuild.currentResult}

Project: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}

Report is attached (ZIP file).
Open Jenkins for full details:
${env.BUILD_URL}
""",
                attachmentsPattern: "report.zip"
            )
        }
    }
}
