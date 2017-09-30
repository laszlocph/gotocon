pipeline {
    agent { docker 'gradle:4.2.0-jdk8-alpine' }
    stages {
        stage('build') {
            steps {
                sh 'gradle clean build'
            }
        }
    }
}