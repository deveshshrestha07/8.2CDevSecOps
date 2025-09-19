pipeline {
  agent any
  tools { jdk 'jdk17' }                   // <-- add this
  options { timestamps(); skipDefaultCheckout(true) }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/deveshshrestha07/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') { steps { sh 'npm install' } }
    stage('Run Tests')            { steps { sh 'npm test || true' } }
    stage('Generate Coverage Report') { steps { sh 'npm run coverage || true' } }
    stage('NPM Audit (Security Scan)'){ steps { sh 'npm audit || true' } }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            set -e
            echo "Java in use:"; java -version

            SCANNER_VERSION="5.0.1.3006"
            rm -rf sonar-scanner-* scanner.zip
            curl -sSLo scanner.zip "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SCANNER_VERSION}.zip"
            unzip -q scanner.zip && rm -f scanner.zip
            SCANNER_DIR="$(find . -maxdepth 1 -type d -name 'sonar-scanner-*' | head -n1)"

            "${SCANNER_DIR}/bin/sonar-scanner" -v
            "${SCANNER_DIR}/bin/sonar-scanner" -Dsonar.login="${SONAR_TOKEN}"
          '''
        }
      }
    }
  }
}
