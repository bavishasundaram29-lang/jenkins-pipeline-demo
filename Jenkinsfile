pipeline {
    agent any

    environment {
        JMX_FILE = "jpetstore_jenkins\\SCR01_Jpetstore.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Cloning GitHub repository..."
                git url: "https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git", branch: "main"
            }
        }

        stage('Verify Workspace') {
            steps {
                bat """
                echo ===== FULL WORKSPACE STRUCTURE =====
                dir /s
                """
            }
        }

        stage('Clean Workspace') {
            steps {
                bat """
                echo Cleaning old files...

                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%
                if exist %RESULTS% del /f /q %RESULTS%
                if exist %LOG_FILE% del /f /q %LOG_FILE%

                echo Cleanup completed
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter Test using %JMX_FILE%...

                jmeter -n -t %JMX_FILE% ^
                -l %RESULTS% ^
                -j %LOG_FILE% ^
                -e -o %REPORT_DIR%

                echo JMeter execution completed
                """
            }
        }

        stage('Verify Output') {
            steps {
                bat """
                echo ===== LOG FILE =====
                type %LOG_FILE%

                echo ===== RESULTS SAMPLE =====
                type %RESULTS%
                """
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: '**/*.jtl, **/report/**, **/*.log', fingerprint: true
            }
        }
    }

    post {

        always {
            echo "Sending email notification..."

            emailext (
                to: "bavishasundaram29@gmail.com",
                subject: "JMeter Report - Build ${BUILD_NUMBER}",
                body: """
                Hi,

                Your JMeter execution has completed.

                Build Number: ${BUILD_NUMBER}
                Status: ${currentBuild.currentResult}

                Please check attached reports.

                Thanks
                """,
                attachmentsPattern: "results.jtl, jmeter.log, report/index.html"
            )
        }
    }
}
