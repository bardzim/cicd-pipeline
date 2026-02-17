pipeline {
    agent any

    tools {
        nodejs 'NodeJS-7.8.0'
    }

    environment {
        BRANCH = "${env.BRANCH_NAME}"
        IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:v1.0 .'
                    } else {
                        sh 'docker build -t nodedev:v1.0 .'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh '''
                            docker ps -a --filter "ancestor=nodemain:v1.0" -q | xargs -r docker stop
                            docker ps -a --filter "ancestor=nodemain:v1.0" -q | xargs -r docker rm
                            docker run -d --expose 3000 -p 3000:3000 nodemain:v1.0
                        '''
                    } else {
                        sh '''
                            docker ps -a --filter "ancestor=nodedev:v1.0" -q | xargs -r docker stop
                            docker ps -a --filter "ancestor=nodedev:v1.0" -q | xargs -r docker rm
                            docker run -d --expose 3001 -p 3001:3000 nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }
}
