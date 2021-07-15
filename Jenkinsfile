#!/usr/bin/env groovy

node('rhel8') {
    stage('Checkout repo') {
        deleteDir()
        git url: 'https://github.com/jrichter1/intellij-common-ui-test-library',
                branch: "${sha1}"
    }

    def props = readProperties file: 'gradle.properties'
    def isSnapshot = props['projectVersion'].contains('-SNAPSHOT')

    stage('Build') {
        sh "./gradlew build"
    }

    stage('Deploy') {
        withCredentials([usernamePassword(credentialsId: 'Nexus-IJ-Credentials', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
            if (isSnapshot) {
                sh "./gradlew publish -PnexusUser=${USER} -PnexusPassword=${PASSWORD}"
            } else {
                sh "./gradlew publish closeAndReleaseJbossNexusStagingRepository -PnexusUser=${USER} -PnexusPassword=${PASSWORD}"
            }
        }
    }
}
