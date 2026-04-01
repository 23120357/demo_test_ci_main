pipeline {
  agent {
    docker {
      image 'python:3.10'
      args '--user=root'
    }
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install dependencies') {
      steps {
        sh '''
python -m pip install --upgrade pip
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
'''
      }
    }

    stage('Lint') {
      steps {
        sh '''
pip install ruff pytest coverage
ruff --format=github --target-version=py310 . || true
'''
      }
    }

    stage('Test') {
      steps {
        sh '''
coverage run -m pytest -v -s
'''
      }
    }

    stage('Coverage Report') {
      steps {
        sh '''
coverage report -m
coverage xml -i -o coverage.xml || true
'''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'coverage.xml, htmlcov/**', allowEmptyArchive: true
      cleanWs()
    }
  }
}
