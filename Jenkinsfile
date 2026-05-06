pipeline {
    agent any

    stages {

        stage('Run JMeter Test') {
            steps {
                bat """
                jmeter -n -t test.jmx -l results.jtl -e -o report
                """
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'report/**', fingerprint: true
            }
        }
    }
}
