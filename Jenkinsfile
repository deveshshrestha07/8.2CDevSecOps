pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/<your_github_username>/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        // Allow pipeline to continue even if tests fail
        sh 'npm test || true'
      }
    }

    stage('Generate Coverage Report') {
      steps {
        // Some projects don’t have a coverage script—keep going anyway
        sh 'npm run coverage || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        // Show known CVEs; don’t fail the whole build
        sh 'npm audit || true'
      }
    }
  }
}
