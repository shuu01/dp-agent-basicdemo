#!groovy

def isPullRequest = env.CHANGE_ID ? true : false
def BuildBadge = addEmbeddableBadgeConfiguration(id: "build", subject: "Build")

pipeline {

  agent any

  stages {

    stage('Checkout') {

      when {
        changeRequest target "feat/*"
      }

      steps {
        script {
          echo """\
          Current branch is ${env.BRANCH_NAME}
          Pull request: merge ${env.CHANGE_BRANCH} into ${env.CHANGE_TARGET}
          Pull request id: ${pullRequest.id} or ${env.CHANGE_ID}
          Pull request title: ${pullRequest.title} or ${env.CHANGE_TITLE}
          Pull request headRef: ${pullRequest.headRef} or ${env.CHANGE_BRANCH}
          Pull request base: ${pullRequest.base} of ${env.CHANGE_TARGET}
          """
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
        expression {
          return env.CHANGE_TARGET =~ "feat/*"
        }
        beforeAgent true
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
        }
      }

    }

    stage('Codestyle Test') {

      agent {
        docker {
          image 'alpine/flake8'
          args '--entrypoint ""'
        }
      }

      when {
        changeRequest()
        branch pattern: "feat/*"
        beforeAgent true
      }

      steps {
        sh 'flake8 --max-line-length=120 skills'
      }
    }

    stage('Api Test') {

      agent {
        docker {
          image 'docker/compose:latest'
          args '--entrypoint ""'
        }
      }

      when {
        changeRequest()
        branch pattern: "feat/*"
        beforeAgent true
      }

      environment {
        HOST = 'agent'
        PORT = '4242'
        TEST = 'agent'
      }

      steps {
        script {
          sh 'docker-compose up --build -d'
          app.inside("-e HOST -e PORT -e TEST --network=dp") { d->
            sh 'python /src/test_server.py'
          }
        }
      }

      post {
        always {
          sh 'docker-compose down'
        }
      }

    }
  }

  post {
    success {
      script {
        pullRequest.setLabels(['Success'])
      }
    }
    failure {
      script {
        pullRequest.setLabels(['Failure'])
      }
    }
  }
}
