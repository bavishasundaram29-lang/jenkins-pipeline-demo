pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git'
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo Cleaning old report folder...

                IF EXIST report rmdir /S /Q report
                IF EXIST results.jtl del /Q results.jtl

                echo Running JMeter test...
                jmeter -n -t test.jmx -l results.jtl -e -o report
                '''
            }
        }
    }

    post {
        always {
            echo 'Publishing HTML Report to Jenkins UI...'

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
