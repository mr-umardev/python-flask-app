pipeline {
  agent {
    docker {
      image 'python:3.11-slim'
      args '-u root:root'
    }
  }

  environment {
    DEPLOY_DIR = "${WORKSPACE}/python-app-deploy"
  }

  stages {

    stage('Build') {
      steps {
        echo 'Installing dependencies...'
        sh '''
          python -V
          pip install --upgrade pip
          pip install -r requirements.txt
        '''
      }
    }

    stage('Test') {
      steps {
        echo 'Running unit tests...'
        sh 'python -m unittest discover -s . -v'
      }
    }

    stage('Deploy') {
      steps {
        echo 'Mock deploying the application...'
        sh '''
          mkdir -p "$DEPLOY_DIR"
          cp app.py "$DEPLOY_DIR/"
          cp requirements.txt "$DEPLOY_DIR/"
        '''
      }
    }

    stage('Run Application') {
      steps {
        echo 'Starting Flask app in background...'
        sh '''
          nohup python "$DEPLOY_DIR/app.py" > "$DEPLOY_DIR/app.log" 2>&1 &
          echo $! > "$DEPLOY_DIR/app.pid"
          sleep 3
        '''
      }
    }

    stage('Integration Test') {
      steps {
        echo 'Testing running Flask app...'
        sh '''
          apt-get update -y && apt-get install -y curl
          curl -s http://127.0.0.1:5000/
        '''
      }
    }

    stage('Cleanup') {
      steps {
        echo 'Stopping app...'
        sh '''
          kill $(cat "$DEPLOY_DIR/app.pid") || true
          rm -rf "$DEPLOY_DIR"
        '''
      }
    }
  }

  post {
    success {
      echo 'Pipeline SUCCESS!'
    }
    failure {
      echo 'Pipeline FAILED!'
    }
  }
}
