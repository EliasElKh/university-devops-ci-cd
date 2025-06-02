pipeline {
    agent none

    triggers {
        githubPush()
    }

    stages {
        stage('Build and Test') {
            agent {
                label 'windows'
            }
            stages {
                stage('Checkout') {
                    steps {
                        checkout scm
                    }
                }

                stage('Setup Node and Install') {
                    steps {
                        bat 'npm ci'
                    }
                }

                stage('Test') {
                    steps {
                        bat 'npm test'
                    }
                }

                stage('Build') {
                    steps {
                        bat 'npm run build'
                    }
                }

                stage('Archive Artifact') {
                    steps {
                        archiveArtifacts artifacts: 'build/**', allowEmptyArchive: false
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                label 'linux'
            }
            when {
                allOf {
                    branch 'main'
                    expression { return env.CHANGE_ID == null }
                }
            }
            steps {
                withCredentials([string(credentialsId: 'render-deploy-hook', variable: 'DEPLOY_HOOK')]) {
                    sh 'curl -X POST "$DEPLOY_HOOK"'
                }
            }
        }
    }
}

