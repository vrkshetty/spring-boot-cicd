pipeline {
    environment {
      version = "${env.BUILD_NUMBER}"
      filename = "app-${env.BUILD_NUMBER}.jar"

    }
    agent any
    stages {
      stage ('build & publish'){

      steps {
          withAWS(credentials: 'aws-credentials', region: 'eu-central-1') {
              sh "chmod +x gradlew"
              sh "./gradlew -Pversion=test build"
              sh "ls -ltr build/libs/"
              s3Upload acl: 'Private', bucket: 'neo-airlines-artifact', file: 'build/libs/demo-test.jar', path: 'app.jar'
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
          s3Download bucket: 'neo-airlines-artifact', file: 'app.jar', path: 'app.jar'
          sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=docker.io/vrkshetty/neo-demo:latest'


          }
          }
      }
    }

}
