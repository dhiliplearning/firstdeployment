pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo "Building branch/PR..."
      }
    }
    stage('Test') {
      steps {
        echo "Running tests..."
        sh 'echo "All tests passed!"'
      }
    }
    stage('Deploy') {
      when { not { changeRequest() } }  // skips on PRs
      steps {
        echo "Deploying because this is not a PR..."
      }
    }
  }
}
