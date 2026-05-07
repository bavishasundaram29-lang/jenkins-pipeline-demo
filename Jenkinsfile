pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
                echo "Code checkout completed"
            }
        }

        stage('Clean Old Files') {
            steps {
                bat '''
                if exist report rmdir /s /q report
                if exist results.jtl del /f /q results.jtl
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat ^
                -n ^
                -t jpetstore_jenkins\\SCR01_Jpetstore.jmx ^
                -l results.jtl ^
                -Jjmeter.save.saveservice.output_format=csv ^
                -e ^
                -o report
                """
            }
        }

        stage('Publish Report') {
            steps {
                publishHTML(target: [
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }

    post {
        success {
            echo "JMeter report published successfully in Jenkins UI"
        }

        failure {
            echo "Build failed. Check logs."
        }

        always {
            archiveArtifacts artifacts: 'results.jtl', fingerprint: true
        }
    }
}
