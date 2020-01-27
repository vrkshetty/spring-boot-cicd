pipeline {
    environment {
      version = "${env.BUILD_NUMBER}"
    }
    agent none
    stages {
        stage('hello AWS') {
            steps {
                withAWS(credentials: 'aws-credentials', region: 'eu-central-1') {
                    sh 'echo "hello KB">hello.txt'
                    s3Upload acl: 'Private', bucket: 'neo-airlines-artifact', file: 'hello.txt'
                    s3Download bucket: 'neo-airlines-artifact', file: 'downloadedHello.txt', path: 'hello.txt'
                    sh 'cat downloadedHello.txt'
                }
            }
        }



        stage('build & push') {
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


            steps {
              sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/myorg/myimage'
            }
        }
    }
}
