pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo.git'
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
