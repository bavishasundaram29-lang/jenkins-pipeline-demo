pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git'
            }
        }

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
