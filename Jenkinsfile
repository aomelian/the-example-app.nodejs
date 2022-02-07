def branch
def revision
def tagVar

pipeline {
    agent {
        node {
            label 'linux'
        }
    }

    options {
        skipDefaultCheckout true
    }

    environment {
        // ECR_PASS = credentials('ecr_password')
        DOCKER_HOST = "tcp://127.0.0.1:2376"
        HOME = "."
    }

    stages {
        stage ('checkout') {
            steps {
                script {
                    def repo = checkout scm
                    revision = sh(script: 'git log -1 --format=\'%h.%ad\' --date=format:%Y%m%d-%H%M | cat', returnStdout: true).trim()
                    branch = repo.GIT_BRANCH.take(20).replaceAll('/', '_')
                    if (branch != 'master') {
                        revision += "-${branch}"
                    }
                    sh "echo 'Building revision: ${revision}'"
                }
            }

        }
        stage ('compile') {
            agent { docker 'node:latest' }
            steps {
                sh 'ls && pwd'
                sh 'HOME=. npm i'
            }
        }
        stage ('build') {
            steps {
                sh "docker build -t ${branch}-${revision} ."
            }
        }
    }
}
