pipeline {
    agent any

    environment {
        JMX_FILE = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS_FILE = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: "https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git", branch: "main"
            }
        }

        stage('Clean Workspace') {
            steps {
                bat """
                echo Cleaning workspace...

                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%
                if exist %RESULTS_FILE% del /f /q %RESULTS_FILE%
                if exist %LOG_FILE% del /f /q %LOG_FILE%

                echo Cleanup completed
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter...

                jmeter -n -t %JMX_FILE% ^
                -l %RESULTS_FILE% ^
                -j %LOG_FILE%
                """
            }
        }

        stage('Force Clean Report Folder') {
            steps {
                bat """
                echo Ensuring clean report folder...

                if exist %REPORT_DIR% (
                    rmdir /s /q %REPORT_DIR%
                )

                mkdir %REPORT_DIR%
                """
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat """
                echo Generating HTML Report...

                jmeter -g %RESULTS_FILE% -o %REPORT_DIR%

                echo Report generated successfully
                dir %REPORT_DIR%
                """
            }
        }

        stage('Publish Report in Jenkins UI') {
            steps {
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

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'results.jtl, jmeter.log, report/**'
            }
        }
    }
}
