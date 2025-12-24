@Library("Jascom@afguliyevv-patch-1") _   // loads your PR version of the shared lib

pipeline {
  agent any
  environment {
    BLACKDUCK_URL = credentials('blackduck-url-string')   // Secret Text holding the full Black Duck URL
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('SCA: Black Duck (soft)') {
      steps {
        blackduckDetect(
          bdUrl: env.BLACKDUCK_URL,
          bdTokenCredId: 'blackduck-api-key',   // Secret Text API token
          scanMode: 'Auto',                    // Rapid on PRs; Intelligent on non-PR builds
          failSeverities: 'NONE',              // Do not fail builds on policy violations
          soft: true,                          // Mark UNSTABLE on non-zero exit
          detectArgs: '''
            --detect.risk.report.json=true
            --detect.risk.report.json.path=reports/blackduck-risk-sca.json
            --detect.notices.report=true
            --detect.notices.report.path=reports
          '''
        )
      }
    }
  }
  post {
    always {
      // Archive the specific risk report and any NOTICES files generated
      archiveArtifacts artifacts: 'reports/blackduck-risk-sca.json, reports/NOTICES*', allowEmptyArchive: true
    }
  }
}
