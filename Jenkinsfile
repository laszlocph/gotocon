pipeline {
    agent none
    stages {
        stage('gradle-build') {
            agent { docker 'gradle:4.2.0-jdk8-alpine' }
            steps {
                sh 'gradle clean build'
                stash includes: 'build/libs/gotocon-1.0-SNAPSHOT.jar', name: 'gotocon-jar'
            }
        }
        stage('docker-build-and-push') {
            agent {
                docker {
                    image 'docker'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            environment {
                DOCKERHUB = credentials('dockerhub')
            }
            steps {
                unstash 'gotocon-jar'
                sh 'cp build/libs/gotocon-1.0-SNAPSHOT.jar docker/'
                sh 'docker build -t laszlocloud/gotocon docker/'
                sh 'docker login -u DOCKERHUB_USR -p DOCKERHUB_PSW'
                sh 'docker push laszlocloud/gotocon'
            }
        }
        stage('deploy') {
            agent {
                docker {
                    image 'docker'
                }
            }

            steps {
                sh 'echo Deploying'
            }
        }
    }
}
