pipeline {
    environment {
      version = "${env.BUILD_NUMBER}"
      filename = "app-${env.BUILD_NUMBER}.jar"

    }
    agent any
    stages {
      stage ('Build '){

      steps {
          withAWS(credentials: 'aws-credentials', region: 'eu-central-1') {
              sh "chmod +x gradlew"
              sh "./gradlew -Pversion=test build"
              sh "ls -ltr build/libs/"
              s3Upload acl: 'Private', bucket: 'neo-airlines-artifact', file: 'build/libs/demo-test.jar', path: 'app.jar'
          }
      }

      }
      stage ('Docker publish'){
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
    volumeMounts:
      - name: docker-config1
        mountPath: /kaniko/.docker/
        volumes:
    volumes:      
    - name: docker-config
      configMap:
          name: docker-config
       """
       }
          }


          steps {withAWS(credentials: 'aws-credentials', region: 'eu-central-1'){
          s3Download bucket: 'neo-airlines-artifact', file: 'app.jar', path: 'app.jar'
          sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=neo-demo:latest'


          }
          }
      }
      stage ('Deploy') {
      steps{
      sh 'echo "deployed"'
      }
      }
    }

}
