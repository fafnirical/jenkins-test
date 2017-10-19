pipeline {
  agent any

  tools {
    nodejs '6.x'
  }

  stages {
    stage('Pre-build') {
      steps {
        step([
          $class: 'GitHubSetCommitStatusBuilder',
          contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'ci/jenkins/build-status'],
        ])
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

    stage('Lint') {
      parallel {
        stage('Lint JavaScript') {
          steps {
            sh './node_modules/.bin/eslint --format=junit --output-file tests/results/eslint.xml'
          }
          post {
            always {
              junit testResults: 'tests/results/eslint.xml'
            }
          }
        }
      }
    }
  }

  post {
    success {
      step([
        $class: 'GitHubCommitStatusSetter',
        contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'ci/jenkins/build-status'],
        errorHandlers: [[$class: 'ChangingBuildStatusErrorHandler', result: 'FAILURE']],
        statusResultSource: [$class: 'ConditionalStatusResultSource', results: [[$class: 'AnyBuildResult', message: 'Build succeeded', state: 'SUCCESS']]],
      ])
    }
    failure {
      step([
        $class: 'GitHubCommitStatusSetter',
        contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'ci/jenkins/build-status'],
        errorHandlers: [[$class: 'ChangingBuildStatusErrorHandler', result: 'FAILURE']],
        statusResultSource: [$class: 'ConditionalStatusResultSource', results: [[$class: 'AnyBuildResult', message: 'Build failed', state: 'FAILURE']]],
      ])
    }
  }
}
