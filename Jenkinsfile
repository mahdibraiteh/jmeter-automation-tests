pipeline {
  agent any
  environment {
    JMETER_HOME = '/opt/apache-jmeter-5.6.3'
    PATH = "${env.JMETER_HOME}/bin:${env.PATH}"
    RESULTS_DIR = 'results'
  }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Prepare') {
      steps { sh 'rm -rf ${RESULTS_DIR} && mkdir -p ${RESULTS_DIR}' }
    }
    stage('Run JMeter') {
      steps {
        sh '''
          jmeter -n -t Test-blazemeter.jmx -p jenkins.properties \
                 -l ${RESULTS_DIR}/results.jtl -e -o ${RESULTS_DIR}/report
        '''
      }
    }
    stage('Publish') {
      steps {
        archiveArtifacts artifacts: 'results/**', fingerprint: true
        publishHTML([
          reportDir: "${RESULTS_DIR}/report",
          reportFiles: 'index.html',
          reportName: 'JMeter HTML Report'
        ])
      }
    }
  }
}
