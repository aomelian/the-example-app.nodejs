def branch
def revision
def tagVar

pipeline {
    agent none

    options {
        skipDefaultCheckout true
    }

    environment {
        // ECR_PASS = credentials('ecr_password')
        registryIp = ""
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
                sh 'npm i'
            }
        }
    }
}
