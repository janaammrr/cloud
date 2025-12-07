pipeline {
  agent any

  environment {
    FIREBASE_TOKEN = credentials('FIREBASE_TOKEN')
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/janaammrr/cloud.git'
      }
    }

    stage('Ensure Node & Firebase CLI') {
      steps {
        sh '''
          if ! command -v node >/dev/null 2>&1; then
            echo "Node not found; please install Node on this agent."
            exit 1
          fi
          if ! command -v firebase >/dev/null 2>&1; then
            echo "Installing firebase-tools..."
            npm install -g firebase-tools
          fi
          firebase --version
        '''
      }
    }

    stage('Install Dependencies (if any)') {
      steps {
        sh '''
          if [ -f package.json ]; then
            npm install
          else
            echo "No package.json — skipping npm install"
          fi
        '''
      }
    }

    stage('Build (optional)') {
      steps {
        sh '''
          if grep -q "\"build\"" package.json 2>/dev/null; then
            npm run build
          else
            echo "No build script; skipping build"
          fi
        '''
      }
    }

    stage('Firebase Deploy') {
      steps {
        sh "firebase deploy --token $FIREBASE_TOKEN --only hosting"
      }
    }
  }
}
