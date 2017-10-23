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
        stage('Lint CSS/SCSS') {
          steps {
            sh './node_modules/.bin/stylelint **/*.{css,scss} --custom-formatter node_modules/stylelint-formatter-relative-junit > tests/results/stylelint.junit.xml'
          }
        }
        stage('Lint JavaScript') {
          steps {
            sh './node_modules/.bin/eslint **/*.{js,jsx} --format node_modules/eslint-formatter-relative-junit --output-file tests/results/eslint.junit.xml'
          }
        }
        stage('Lint TypeScript') {
          steps {
            sh './node_modules/.bin/tslint **/*.{ts,tsx} --exclude "node_modules/**" --format junit --formatters-dir node_modules/tslint-junit-formatter/formatters --out tests/results/tslint.junit.xml'
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
    php '7.1'
  }
}
