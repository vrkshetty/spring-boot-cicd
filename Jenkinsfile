pipeline {
    environment {
      version = "${env.BUILD_NUMBER}"

    }
    agent any
    stages {
      stage ('build & publish'){

      steps {
          withAWS(credentials: 'aws-credentials', region: 'eu-central-1') {
              sh "chmod +x gradlew"
              sh "./gradlew -Pversion=test build"
              s3Upload acl: 'Private', bucket: 'neo-airlines-artifact', file: 'app.jar', path: 'app-${version}.jar'
              sh 'ls -ltr'
          }
      }

      }
      stage ('Docker build'){
          agent {
          kubernetes {
            //cloud 'kubernetes'
            defaultContainer 'kaniko'
            yaml """
       kind: Pod
       spec:
        containers:
        - name: kaniko
          image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
          imagePullPolicy: Always
          command:
          - /busybox/cat
          tty: true
       """
       }
          }


          steps {withAWS(credentials: 'aws-credentials', region: 'eu-central-1'){
          s3Download bucket: 'neo-airlines-artifact', file: 'downloadedHello.txt', path: 'hello.txt'
          sh 'cat downloadedHello.txt'

          }
          }
      }
    }

}
