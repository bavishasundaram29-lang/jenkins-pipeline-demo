pipeline {
    agent any

    environment {
        REPORT_DIR = "${WORKSPACE}"
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-repo/your-project.git'
            }
        }

        stage('Run Tests / Build') {
            steps {
                echo "Running build or JMeter tests..."
                
                // Example for JMeter (uncomment if needed)
                // bat 'jmeter -n -t test.jmx -l result.jtl -e -o report'
            }
        }

        stage('Generate Report Check') {
            steps {
                echo "Checking report files..."
                bat 'dir'
            }
        }
    }

    post {

        always {
            script {
                emailext(
                    to: "bavishasundar@gmail.com",
                    subject: "Jenkins Build Report - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: """
Hello,

Build Completed.

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}

Please find attached reports (if generated).

Thanks,
Jenkins
""",

                    attachmentsPattern: "**/*.html, **/*.jtl, **/index.html"
                )
            }
        }
    }
}
