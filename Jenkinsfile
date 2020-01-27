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
            sh "curl -o app.jar http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/repository/maven-releases/com/example/demo/test/demo-${version}.jar"
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
              sh 'curl --silent --output app.jar http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/repository/maven-releases/com/example/demo/test/demo-${version}.jar'
              sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/myorg/myimage'
            }
        }
    }
}
