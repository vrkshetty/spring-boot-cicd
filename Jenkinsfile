pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                sh "./gradlew -Pversion=${version} build"
            }
        }
    }
}
