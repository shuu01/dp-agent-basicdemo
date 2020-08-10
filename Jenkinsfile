#!groovy

def isPullRequest = env.CHANGE_ID ? true : false
def BuildBadge = addEmbeddableBadgeConfiguration(id: "build", subject: "Build")

pipeline {

agent any

  stages {

    stage('Checkout') {
      when {
        changeRequest target: "feat/*", comparator: 'REGEXP'
      }
      steps {
        echo "Current branch is ${env.BRANCH_NAME}"
        echo "This is a pull request: merge ${env.CHANGE_BRANCH} into ${env.CHANGE_TARGET}"
        echo "Pull request id: ${pullRequest.id}"
      }
    }

    stage('Build') {

      agent {
        dockerfile {
          filename 'Dockerfile'
          dir 'skills/valentines_day_skill'
          customWorkspace 'skills/valentines_day_skill'
        }
      }

      when {
        branch pattern: "feat/*", comparator: 'REGEXP'
      }

      environment {
        HOST = 'localhost'
        PORT = '3000'
      }

      steps {
        sh 'python /src/test_server.py'
      }
    }

    stage('Test') {
      steps {
        echo 'Testing..'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploying....'
      }
    }
  }
}
