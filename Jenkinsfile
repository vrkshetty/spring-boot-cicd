def version = "${env.BUILD_NUMBER}"

node {
    stage('Checkout') {
        scm checkout
    }


    stage 'Build'
    sh "./gradlew -Pversion=${version} build"

}
