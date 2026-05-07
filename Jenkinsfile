pipeline {
    agent any

    stages {

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo Workspace files:
                dir

                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl

                echo Running JMeter test...

                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl -e -o report
                '''
            }
        }
    }

    post {
        always {
            script {
                if (fileExists('report/index.html')) {
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'report',
                        reportFiles: 'index.html',
                        reportName: 'JMeter HTML Report'
                    ])
                } else {
                    echo "Report not generated"
                }
            }
        }
    }
}
