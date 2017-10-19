pipeline {
  agent any
  stages {
    stage('Dependencies') {
      failFast true
      parallel {
        stage('Install Node.js dependencies') {
          steps {
            sh 'npm install --no-progress'
          }
        }
      }
    }
    stage('Linting') {
      parallel {
        stage('Lint JavaScript') {
          steps {
            sh './node_modules/.bin/eslint . --format="./node_modules/eslint-formatter-relative-junit" --output-file tests/results/eslint.junit.xml'
          }
        }
      }
    }
    stage('Build') {
      steps {
        echo 'This shouldn\'t display'
      }
    }
  }
  post {
    always {
      junit(testResults: 'tests/results/*.junit.xml', allowEmptyResults: true)
    }
  }
  tools {
    nodejs '6.x'
  }
}
