pipeline {
    agent any

    stages {

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo Cleaning old data...
                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl

                echo Running JMeter Test...
                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl

                echo Generating HTML Report...
                jmeter -g results.jtl -o report

                echo Checking report folder...
                dir report
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
