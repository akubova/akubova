pipeline {
  agent any
  stages {
    stage('stage1') {
      steps {
        echo 'This is build $BUILD_NUMBER of demo'
        sh 'echo "This is build $BUILD_NUMBER of demo"'
      }
    }

  }
  environment {
    DEMO = '1'
  }
}