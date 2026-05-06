pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                // Jenkins will automatically checkout from GitHub (no need to duplicate)
                echo "Code checkout completed"
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                C:\\apache-jmeter-5.6.3\\bin\\jmeter.bat -n -t test.jmx -l results.jtl -e -o report
                """
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'report/**', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "JMeter test executed successfully. Report generated."
        }
        failure {
            echo "Build failed. Please check errors."
        }
    }
}
