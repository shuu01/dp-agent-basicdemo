pipeline {

agent none

  stages {

    stage('Build') {

      agent {
        dockerfile {
          filename 'Dockerfile'
          dir 'skills/valentines_day_skill'
        }
      }

      when {
        branch 'feat/basic-alexa-demo'
      }

      environment {
        HOST = 'localhost'
        PORT = '3000'
      }

      steps {
        sh 'python test_server.py'
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
