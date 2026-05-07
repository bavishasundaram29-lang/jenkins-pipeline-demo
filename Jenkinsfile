pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Running build..."
            }
        }

        stage('Run Tests / Build') {
            steps {
                echo "Running tests or JMeter..."
            }
        }

        stage('Generate Report Check') {
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
Build Number: ${env.BUILD_NUMBER}
Status: ${currentBuild.currentResult}
""",
                attachmentsPattern: "**/*.html, **/*.jtl, **/index.html"
            )
        }
    }
}
