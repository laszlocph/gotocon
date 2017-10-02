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
                sh 'docker build -t laszlocloud/gotocon: docker/'
                sh 'docker login -u $DOCKERHUB_USR -p $DOCKERHUB_PSW'
                sh 'docker push laszlocloud/gotocon'
                sh 'echo $GIT_COMMIT'
            }
        }
        stage('deploy') {
            agent {
                docker {
                    image 'laszlocloud/kubectl'
                }
            }
            environment {
                K8S_TOKEN = credentials('k8s-token')
            }
            steps {
                sh 'kubectl apply -f gotocon.yml --server https://10.0.0.237:6443 --insecure-skip-tls-verify=true --token $K8S_TOKEN'
                sh 'kubectl rollout status deployment/gotocon -w --server https://10.0.0.237:6443 --insecure-skip-tls-verify=true --token $K8S_TOKEN'
            }
        }
    }
}
