pipeline {
    agent any

    stages {

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo Workspace before run:
                dir

                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl

                echo Running JMeter test...

                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl

                echo Generating HTML report...

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
