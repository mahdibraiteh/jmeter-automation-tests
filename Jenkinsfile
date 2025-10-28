pipeline {
    agent any

    tools {
        nodejs 'NodeJS_20' // Must match the NodeJS installation name in Jenkins > Global Tool Configuration
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        ansiColor('xterm') // colored console logs
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📦 Checking out repository...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '📦 Installing npm dependencies...'
                bat 'npm ci'
            }
        }

        stage('Run WebdriverIO Tests') {
            steps {
                echo '🚀 Running WebdriverIO tests...'
                // bat 'npx wdio run ./wdio.conf.js'

                bat 'npx wdio run ./wdio.conf.js --spec ./test/specs/test1.spec.js '
            }
        }

        stage('Publish Test Reports') {
            steps {
                echo '🧾 Publishing test reports...'
                
                // JUnit test reports (works if WDIO reporter is set to output ./junit-reports/*.xml)
                junit allowEmptyResults: true, testResults: 'junit-reports/**/*.xml'
            }
        }

        stage('Allure Report') {
            when {
                expression { fileExists('allure-results') }
            }
            steps {
                echo '✨ Generating Allure report...'
                allure([
                    includeProperties: false,
                    jdk: '',
                    results: [[path: 'allure-results']]
                ])
            }
        }
    }

    post {
        success {
            echo '✅ All tests passed successfully!'
        }
        failure {
            echo '❌ Some tests failed. Please check the test reports.'
        }
        always {
            echo '📊 Build completed. Check reports under Jenkins > Build Details.'

            // Archive WebdriverIO artifacts
            archiveArtifacts artifacts: 'errorShots/**/*.png', allowEmptyArchive: true
            archiveArtifacts artifacts: 'logs/**/*.log', allowEmptyArchive: true
        }
    }
}