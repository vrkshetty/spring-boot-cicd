pipeline {
     agent { label 'master' }
    stages {
        stage('build') {
            steps {
                sh "chmod +x gradlew"
                sh "./gradlew -Pversion=test build"
            }
        }
    }
}
