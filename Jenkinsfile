pipeline {
    environment {
      version = "${env.BUILD_NUMBER}"
    }
    agent none
    stages {
        stage('Build') {
            agent any
            steps {
            sh "chmod +x gradlew"
            sh "./gradlew -Pversion=test build"

            }

            }


        stage ('Publish'){
            steps {withAWS(credentials: 'aws-credentials', region: 'eu-central-1')
            {
            s3Upload (acl:"Private", bucket:"neo-airlines-artifact", file:"demo-${version}.txt")
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
