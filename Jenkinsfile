pipeline {
  agent any
  stages {
    stage('Pre-build') {
      agent any
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
            agent any
            steps {
              sh 'npm install --no-progress'
            }
          }
        }
      }
      stage('Linting') {
        parallel {
          stage('Lint JavaScript') {
            agent any
            steps {
              sh './node_modules/.bin/eslint . --format=junit --output-file tests/results/eslint.junit.xml'
              junit(testResults: 'tests/results/eslint.junit.xml', allowEmptyResults: true)
            }
            post {
              always {
                checkstyle(pattern: 'tests/results/*.checkstyle.xml')
                
              }
              
            }
          }
        }
      }
    }
    tools {
      nodejs '6.x'
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