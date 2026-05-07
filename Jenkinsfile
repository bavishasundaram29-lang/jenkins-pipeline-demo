pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3"   // change if needed
        TEST_PLAN = "test.jmx"
        RESULTS = "results.jtl"
        REPORT_DIR = "report"
        LOG_FILE = "jmeter.log"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repo/your-project.git'
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                echo Running JMeter Test...

                %JMETER_HOME%\\bin\\jmeter.bat -n ^
                -t %TEST_PLAN% ^
                -l %RESULTS% ^
                -j %LOG_FILE% ^
                -e -o %REPORT_DIR%

                echo Test Execution Completed
                """
            }
        }

        stage('Verify Outputs') {
            steps {
                bat """
                echo ===== LOG FILE =====
                type %LOG_FILE%

                echo ===== SAMPLE JTL =====
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
            echo "Sending Email with Report..."

            emailext (
                subject: "JMeter Test Report - Build ${BUILD_NUMBER}",
                body: """
                    Hi Team,

                    JMeter execution completed.

                    Build Number: ${BUILD_NUMBER}
                    Status: ${currentBuild.currentResult}

                    Please find the attached report/logs.

                    Thanks
                """,
                to: "yourmail@gmail.com",
                attachmentsPattern: "results.jtl, jmeter.log, report/index.html"
            )
        }
    }
}
