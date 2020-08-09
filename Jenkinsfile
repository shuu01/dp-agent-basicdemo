#!groovy

def isPullRequest = env.CHANGE_ID ? true : false

node {

  def app

  stage('checkout') {
    checkout scm

    echo "Current branch is ${env.BRANCH_NAME}"

    if (isPullRequest) {
      echo "This is a pull request: merge ${env.CHANGE_BRANCH} into ${env.CHANGE_TARGET}"
    }
  }

  stage('build') {
    app = docker.build('demo:latest', '-f ./skills/valentines_day_skill/Dockerfile ./skills/valentines_day_skill')
  }

  stage('module test') {

    //sh 'docker network create dp || true'

    app.withRun('--name server') { c ->
      docker.image('alpine').inside("--link ${c.id}:server -e HOST=server -e PORT=8000") { d ->
        sh 'while ! nc -z $HOST $PORT; do sleep 1; done'
      }
      app.inside("-e HOST=server -e PORT=8000 -e TEST=skill --link ${c.id}:server") { d ->
        sh 'python /src/test_server.py'
      }
    }

    //sh 'docker network rm dp'

  }

  stage('codestyle test') {
    docker.image('alpine/flake8').inside('--entrypoint ""') { c ->
      sh 'flake8 --max-line-length=120 skills'
    }
  }

  stage('api test') {

    docker.image('docker/compose:latest').inside('--entrypoint ""') { c ->
      sh 'docker-compose down || true'
      sh 'docker-compose up --build -d'
    }
    app.inside("-e HOST=agent -e PORT=4242 -e TEST=agent --network=dp") { d->
      sh 'python /src/test_server.py'
    }

    docker.image('docker/compose:latest').inside('--entrypoint ""') { c ->
      sh 'docker-compose down'
    }
  }

}
