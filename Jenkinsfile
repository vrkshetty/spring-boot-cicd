pipeline {
agent  {
stage('build') {
  agent any
    steps {
        sh "chmod +x gradlew"
        sh "./gradlew -Pversion=test build"

          }
      }
      }
  stage ('docker build & push') {
  {
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
                sh "curl -o app.jar http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/repository/maven-releases/com/example/demo/test/demo-test.jar"
            }
}
}
