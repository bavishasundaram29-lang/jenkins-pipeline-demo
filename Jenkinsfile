pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                bat '''
                echo Cleaning old data...
                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo Running JMeter Test...

                jmeter -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl

                echo JMeter execution completed
                '''
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat '''
                echo Generating HTML Report...

                IF NOT EXIST results.jtl (
                    echo results.jtl NOT FOUND - FAILING BUILD
                    exit /b 1
                )

                jmeter -g results.jtl -o report

                echo Checking report folder...
                dir report
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
                    echo "HTML report NOT generated - skipping publishHTML"
                }
            }
        }
    }
}
