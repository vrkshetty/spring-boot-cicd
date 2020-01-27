

pipeline {
agent {
   any {
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
     - name: jenkins-docker-cfg
       mountPath: /kaniko/.docker
 volumes:
 - name: jenkins-docker-cfg
   projected:
     sources:
     - secret:
         name: regcred
         items:
           - key: .dockerconfigjson
             path: config.json
"""
   }
 }

    stages {
        stage('build') {
            steps {
                sh "chmod +x gradlew"
                sh "./gradlew -Pversion=test build"

            }

        }
        stage ('Docker-build'){
        steps('Build with Kaniko') {
                sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/myorg/myimage'
              }

        }
        stage ('push'){
            steps {
            sh "curl -o app.jar http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/repository/maven-releases/com/example/demo/test/demo-test.jar"
            }

        }

    }
}
