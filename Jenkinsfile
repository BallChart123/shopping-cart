pipeline {
  agent any
  stages {
    stage('install dependency') {
      steps {
        sh 'cd frontEnd && npm install'
      }
    }

    stage('run unit test') {
      parallel {
        stage('code analysis frontend') {
          steps {
            sh 'cd frontEnd && npm run test'
          }
        }
        stage('code analysis backend') {
          steps {
            sh 'cd backEnd && ./mvnw clean test'
          }
        }
      }
    }

    stage('run build backend') {
      steps {
        sh 'cd backEnd && mvn clean package'
      }
    }

    stage('setup test fixtures') {
      steps {
        sh 'docker-compose up --build -d'
      }
    }

    stage('run UI test') {
      steps {
        sh 'robot atdd/robot-ui/happyToy.robot'
      }
    }

    stage('run API test') {
      steps {
        sh 'robot atdd/api-robot/happyToyApi.robot'
      }
    }

  }
  post {
      always {
          junit 'backEnd/**/target/surefire-reports/TEST-*.xml'
          robot outputPath: 'atdd/robot-ui/', passThreshold: 100.0
          robot outputPath: 'atdd/api-robot/', passThreshold: 100.0
          sh 'docker-compose down -v'
      }
  }
}