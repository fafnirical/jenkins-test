pipeline {
  agent any
  stages {
    stage('Pre-build') {
      steps {
        step([$class: 'GitHubSetCommitStatusBuilder', contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'ci/jenkins/build-status']])
      }
    }
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
            sh './node_modules/.bin/eslint . --format=junit --output-file tests/results/eslint.junit.xml || true'
            junit(testResults: 'tests/results/eslint.junit.xml', allowEmptyResults: true)
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
  tools {
    nodejs '6.x'
  }
}