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

    app.withRun('-p 3000:3000') { c ->
      app.inside('--link ${c.id}:server -e HOST=server -e PORT=3000') { d ->
        sh 'python /src/test_server.py'
      }
    }

  }
}
