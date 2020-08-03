node {

  def app

  stage('clone') {

    checkout scm

  }

  stage('build') {

    sh 'ls -alh'

    app = docker.build('demo:latest', '-f ./skills/valentines_day_skill/Dockerfile ./skills/valentines_day_skill')

  }

  stage('test') {

    sh 'docker network create dp || true'

    app.withRun('--network dp') { c ->
      //docker.image('alpine').inside('--network host -e HOST=localhost -e PORT=8000') { d ->
      //  sh 'while ! nc -z $HOST $PORT; do sleep 1; done'
      //}
      app.inside("-e HOST=${c.id} -e PORT=8000 --network dp") { d ->
        sh 'python /src/test_server.py'
      }
    }

  }
}
