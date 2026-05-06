pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo "Code checkout completed"
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                C:\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat -n -t jpetstore_jenkins/SCR01_Jpetstore.jmx -l results.jtl -e -o report
                """
            }
        }

        stage('Publish Report') {
            steps {
                publishHTML([
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }

    post {
        success {
            echo "JMeter report published in Jenkins UI"
        }
        failure {
            echo "Build failed"
        }
    }
}
