pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Running build..."
            }
        }

        stage('Run JMeter Test') {
            steps {
                echo "Run JMeter here (after fixing branch issue)"
                // bat 'jmeter -n -t test.jmx -l result.jtl -e -o report'
            }
        }

        stage('Verify Report') {
            steps {
                bat 'dir'
            }
        }
    }

    post {
        always {
            emailext(
                to: "bavishasundar@gmail.com",
                subject: "Jenkins Build - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Build Completed

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}
""",
                attachmentsPattern: "**/*.html, **/*.jtl, **/index.html"
            )
        }
    }
}
