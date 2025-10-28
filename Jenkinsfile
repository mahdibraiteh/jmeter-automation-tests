pipeline {
    agent any

    environment {
        // ⚠️ Update this path to where JMeter is installed on your Windows system
        JMETER_HOME = 'C:\\Users\\mahdi\\OneDrive\\Desktop\\apache-jmeter-5.6.3'
        RESULTS_DIR = 'results'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Prepare') {
            steps {
                // Delete old results and create results folder
                bat '''
                if exist %RESULTS_DIR% rd /s /q %RESULTS_DIR%
                mkdir %RESULTS_DIR%
                '''
            }
        }

        stage('Run JMeter') {
            steps {
                // Run JMeter in non-GUI mode, generate HTML report
                bat '''
                "%JMETER_HOME%\\bin\\jmeter.bat" ^
                  -n -t Test-blazemeter.jmx ^
                  -p jenkins.properties ^
                  -l %RESULTS_DIR%\\results.jtl ^
                  -e -o %RESULTS_DIR%\\report

                if %ERRORLEVEL% NEQ 0 exit /b %ERRORLEVEL%
                '''
            }
        }

        stage('Debug Report') {
            steps {
                //List report folder to verify all files are generated
                bat 'dir %RESULTS_DIR%\\report /s'
            }
        }

        stage('Test HTML Access') {
            steps {
                bat '''
                type %RESULTS_DIR%\\report\\index.html
                '''
            }
        }

        stage('Archive Report') {
            steps {
                // Archive entire report folder recursively
                archiveArtifacts artifacts: 'results/report/**', allowEmptyArchive: false, fingerprint: true
            }
        }

        stage('Publish') {
            steps {
                // Archive all results
                // archiveArtifacts artifacts: 'results/**', fingerprint: true

                // Publish HTML report (use forward slashes!)
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: "${RESULTS_DIR}/report",
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'
                ])
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        failure {
            echo 'Build failed. Check JMeter logs and results.'
        }
    }
}