pipeline {
    agent any

    environment {
        REPORT_DIR = "${WORKSPACE}"
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/bavishasundaram29-lang/jenkins-pipeline-demo.git'
            }
        }

        stage('Run Tests / Build') {
            steps {
                echo "Running build or JMeter tests..."

                // If you are using JMeter, uncomment below:
                // bat 'jmeter -n -t test.jmx -l result.jtl -e -o report'
            }
        }

        stage('Generate Report Check') {
            steps {
                echo "Checking workspace files..."
                bat 'dir'
            }
        }
    }

    post {

        always {
            emailext(
                to: "bavishasundar@gmail.com",
                subject: "Jenkins Build Report - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Build Completed Successfully.

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}

Please find attached reports if available.

Thanks,
Jenkins
""",
                attachmentsPattern: "**/*.html, **/*.jtl, **/index.html"
            )
        }
    }
}
