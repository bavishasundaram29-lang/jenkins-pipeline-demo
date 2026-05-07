pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                bat """
                if exist report rmdir /s /q report
                if exist results.jtl del /f /q results.jtl
                if exist jmeter.log del /f /q jmeter.log
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                jmeter -n -t test.jmx ^
                -l results.jtl ^
                -j jmeter.log ^
                -e -o report
                """
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts '**/*'
            }
        }
    }

    post {
        always {
            emailext(
                to: "bavishasundaram29@gmail.com",
                subject: "JMeter Build ${BUILD_NUMBER}",
                body: "Execution completed. Check attached report.",
                attachmentsPattern: "results.jtl, jmeter.log, report/index.html"
            )
        }
    }
}
