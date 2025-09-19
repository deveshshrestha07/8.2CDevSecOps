pipeline {
  agent any
  options { timestamps(); skipDefaultCheckout(true) }

  stages {
    stage('Checkout') {
      steps {
        // Use your real repo URL here
        git branch: 'main', url: 'https://github.com/deveshshrestha07/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps { sh 'npm install' }
    }

    stage('Run Tests') {
      steps { sh 'npm test || true' } // don't fail the whole pipeline on test failures
    }

    stage('Generate Coverage Report') {
      steps { sh 'npm run coverage || true' }
    }

    stage('NPM Audit (Security Scan)') {
      steps { sh 'npm audit || true' }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            set -e

            # Pick a scanner version; update if SonarSource releases a newer one
            SCANNER_VERSION="5.0.1.3006"

            # Download SonarScanner CLI
            rm -rf sonar-scanner-* scanner.zip
            curl -sSLo scanner.zip \
              "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SCANNER_VERSION}.zip"

            # Unpack and locate the folder (works for linux/macos distributions)
            unzip -q scanner.zip
            rm -f scanner.zip
            SCANNER_DIR="$(find . -maxdepth 1 -type d -name 'sonar-scanner-*' | head -n1)"

            # Show version (ensures Java + scanner are OK)
            "${SCANNER_DIR}/bin/sonar-scanner" -v

            # Run analysis. Credentials are taken from SONAR_TOKEN env var.
            # Other settings come from sonar-project.properties in the repo.
            "${SCANNER_DIR}/bin/sonar-scanner" \
              -Dsonar.login="${SONAR_TOKEN}"

            echo "SonarCloud analysis triggered."
          '''
        }
      }
    }
  }
}
