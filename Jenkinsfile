pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git'
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                jmeter -n ^
                -t test.jmx ^
                -l result.jtl ^
                -e ^
                -o report
                '''
            }
        }

        stage('Verify Report') {
            steps {
                bat 'dir report'
            }
        }
    }

    post {
        always {
            emailext(
                to: "bavishasundar@gmail.com",
                subject: "Jenkins Report - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Build Completed

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}

Report generated in workspace/report
""",
                attachmentsPattern: "report/index.html, result.jtl"
            )
        }
    }
}
