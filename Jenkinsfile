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

    app.inside {
      sh 'python /src/test_server.py'
    }

  }
}
