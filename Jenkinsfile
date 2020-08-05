node {

  def app

  stage('clone') {

    checkout scm

  }

  stage('build') {

    sh 'ls -alh'

    app = docker.build('demo:latest', '-f ./skills/valentines_day_skill/Dockerfile ./skills/valentines_day_skill')

  }

  stage('module test') {

    sh 'docker network create dp || true'

    app.withRun('--network dp --name server') { c ->
      docker.image('alpine').inside('--network dp -e HOST=server -e PORT=8000') { d ->
        sh 'while ! nc -z $HOST $PORT; do sleep 1; done'
      }
      app.inside("-e HOST=server -e PORT=8000 -e TEST=skill --network dp") { d ->
        sh 'python /src/test_server.py'
      }
    }

  }

  stage('codestyle test') {
    docker.image('alpine/flake8').inside('--entrypoint ""') { c ->
      sh 'flake8 --max-line-length=120 skills'
    }
  }

  stage('api test') {
    docker.image('docker/compose:latest').withRun('--entrypoint=""') { c ->
      sh 'docker-compose up -d'
      app.inside("-e HOST=agent -e PORT=4242 -e TEST=agent --network=dp") { d->
        sh 'python /src/test_server.py'
      }
    }
  }

}
