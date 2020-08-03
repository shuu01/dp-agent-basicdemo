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

    app.withRun('-p 8000:8000') { c ->
      app.inside('--network host -e HOST=localhost -e PORT=8000') { d ->
        sh 'while ! nc -z $HOST $PORT; do sleep 1; done'
      }
      app.inside('--network host -e HOST=localhost -e PORT=8000') { d ->
        sh 'env'
        sh 'python /src/test_server.py'
      }
    }

  }
}
