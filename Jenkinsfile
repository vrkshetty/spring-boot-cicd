pipeline {
     agent any
    stages {
        stage('build') {
            steps {
                sh "chmod +x gradlew"
                sh "./gradlew -Pversion=test build"
            }
        }
    }
}
