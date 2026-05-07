pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git'
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                jmeter -n -t test.jmx -l results.jtl -e -o report
                '''
            }
        }
    }

    post {
        always {
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
}
