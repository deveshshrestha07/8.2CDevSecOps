pipeline {
  agent any
  options { timestamps(); skipDefaultCheckout(true) }

  stages {
    stage('Checkout') {
      steps {
        // Use YOUR real repo URL
        git branch: 'main', url: 'https://github.com/deveshshrestha07/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        // nodejs-goof uses `snyk test` which needs auth; keep pipeline going
        sh 'npm test || true'
      }
    }

    stage('Generate Coverage Report') {
      steps {
        // If no coverage script exists, this will no-op but won’t fail the build
        sh 'npm run coverage || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        // Show CVEs but don’t fail the build
        sh 'npm audit || true'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            set -e

            # Use Java 17 on macOS for this stage only (avoids JDK tool name mismatches)
            JAVA_HOME="$(/usr/libexec/java_home -v 17)"
            export JAVA_HOME
            export PATH="$JAVA_HOME/bin:$PATH"
            echo "Using Java:"; java -version

            # SonarScanner CLI version (update if SonarSource releases newer)
            SCANNER_VERSION="5.0.1.3006"

            # Download & unpack scanner
            rm -rf sonar-scanner-* scanner.zip
            curl -sSLo scanner.zip "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SCANNER_VERSION}.zip"
            unzip -q scanner.zip && rm -f scanner.zip

            # Locate unpacked dir
            SCANNER_DIR="$(find . -maxdepth 1 -type d -name 'sonar-scanner-*' | head -n1)"

            # Verify scanner (and Java) are OK
            "$SCANNER_DIR/bin/sonar-scanner" -v

            # Run analysis; reads settings from sonar-project.properties
            "$SCANNER_DIR/bin/sonar-scanner" -Dsonar.login="${SONAR_TOKEN}"

            echo "SonarCloud analysis triggered."
          '''
        }
      }
    }
  }
}
