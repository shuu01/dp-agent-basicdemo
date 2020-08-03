node {

  def app

  stage('clone') {

    checkout scm

  }

  stage('build') {

    sh 'ls -alh'

    app = docker.build('demo:latest', '-f Dockerfile ./skills/valentines_day_skill')

  }

  stage('test') {

    app.inside('python /src/test_server.py')

  }
}
