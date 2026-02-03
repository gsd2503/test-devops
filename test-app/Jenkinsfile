pipeline {
    agent any

    tools {
        nodejs 'node18'
    }

    environment {
        DOCKER_IMAGE = "gsd2503/react-test"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/gsd2503/test-devops.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('test-app') {
                    sh 'npm install'
                }
            }
        }

        stage('Build React App') {
            steps {
                dir('test-app') {
                    sh 'npm run build'
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('test-app') {
                    sh 'docker build -t $DOCKER_IMAGE:latest .'
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-jenkins',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    dir('test-app') {
                        sh '''
                        docker login -u $DOCKER_USER -p $DOCKER_PASS
                        docker push $DOCKER_IMAGE:latest
                        '''
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker rm -f react-app || true
                docker run -d -p 3000:80 --name react-app $DOCKER_IMAGE:latest
                '''
            }
        }
    }
}
