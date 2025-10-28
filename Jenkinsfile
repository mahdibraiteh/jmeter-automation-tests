pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\Users\\mahdi\\OneDrive\\Desktop\\apache-jmeter-5.6.3'
        RESULTS_DIR = 'results'
        REPORT_DIR = 'report_tmp' // temporary folder for Jenkins-safe HTML
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Prepare') {
            steps {
                // Clean old results
                bat '''
                if exist %RESULTS_DIR% rd /s /q %RESULTS_DIR%
                if exist %REPORT_DIR% rd /s /q %REPORT_DIR%
                mkdir %RESULTS_DIR%
                '''
            }
        }

        stage('Run JMeter') {
            steps {
                // Run JMeter and generate HTML dashboard
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
                // List all files to confirm HTML report generation
                bat 'dir %RESULTS_DIR%\\report /s'
            }
        }

        stage('Prepare Jenkins HTML Report') {
            steps {
                // Copy entire report folder to a Jenkins-friendly folder
                // preserves all subfolders and assets
                bat '''
                mkdir %REPORT_DIR%
                xcopy %RESULTS_DIR%\\report\\* %REPORT_DIR% /E /I /Y
                '''
            }
        }

        stage('Publish HTML Report') {
            steps {
                // Archive all results as artifacts
                archiveArtifacts artifacts: 'results/**', fingerprint: true

                // Use publishHTML to display the report correctly in Jenkins
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: "${REPORT_DIR}",
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
