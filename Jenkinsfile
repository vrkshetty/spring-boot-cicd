pipeline {
     agent any
    stages {
        stage('build') {
            steps {
                sh "chmod +x gradlew"
                sh "./gradlew -Pversion=test build"

            }

        }
        stage ('Docker-build'){
            steps {
            sh "docker build -t demotest ."
            }

        }
        stage ('push'){
            steps {
            sh "curl -o app.jar http://nexus-sonatype-nexus.tools.svc.cluster.local:8080/repository/maven-releases/com/example/demo/test/demo-test.jar"
            }

        }

    }
}
