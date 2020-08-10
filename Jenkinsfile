#!groovy

def isPullRequest = env.CHANGE_ID ? true : false
def BuildBadge = addEmbeddableBadgeConfiguration(id: "build", subject: "Build")

pipeline {

agent any

  stages {

    stage('Checkout') {
      when {
        changeRequest()
      }
      steps {
        script {
          echo "Current branch is ${env.BRANCH_NAME}"
          echo "This is a pull request: merge ${env.CHANGE_BRANCH} into ${env.CHANGE_TARGET}"
          echo """Pull request id: ${pullRequest.id} or ${env.CHANGE_ID}
                Pull request title: ${pullRequest.title} or ${env.CHANGE_TITLE}
                Pull request headRef: ${pullRequest.headRef}
                Pull request base: ${pullRequest.base}"""
        }
      }
    }

    stage('Module Test') {

      agent {
        docker {
          image 'docker'
        }
      }

      when {
        changeRequest()
      }

      environment {
        HOST = 'server'
        PORT = '8000'
        TEST = 'skill'
      }

      steps {
        script {
          app = docker.build('demo:latest', '-f ./skills/valentines_day_skill/Dockerfile ./skills/valentines_day_skill')
          app.withRun('--name server') { c ->
            app.inside("-e HOST -e PORT -e TEST --link ${c.id}:server") { d ->
              sh 'python /src/test_server.py'
            }
          }
          pullRequest.addLabel('Passing')
        }
      }

    }

    stage('Codestyle Test') {
      agent {
        image 'alpine/flake8'
        args '--entrypoint ""'
      }
      steps {
        sh 'flake8 --max-line-length=120 skills'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploying....'
      }
    }
  }
}
