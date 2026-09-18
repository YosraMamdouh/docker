pipeline {
    agent any

    environment {
        APP_NAME = 'new-app-nti'
        REPO_URL = "https://github.com/YosraMamdouh/docker.git"
    }

    parameters {
        choice(
            name: 'Git_Branch',
            choices: ['main', 'dev', 'staging'],
            description: 'Branch to build'
        )
    }

    stages {

        stage('Getting Repo files') {
            steps {
                dir('app') {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: "*/${params.Git_Branch}"]],
                        userRemoteConfigs: [[
                            url: "${REPO_URL}",
                            credentialsId: 'github'
                        ]]
                    ])
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${APP_NAME}:${BUILD_NUMBER} ./app
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'docker',
                            usernameVariable: 'DOCKER_USERNAME',
                            passwordVariable: 'DOCKER_PASSWORD'
                        )
                    ]) {
                        sh '''
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

                            docker tag ${APP_NAME}:${BUILD_NUMBER} $DOCKER_USERNAME/${APP_NAME}:${BUILD_NUMBER}

                            docker push $DOCKER_USERNAME/${APP_NAME}:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh """
                        docker rm -f ${APP_NAME}-${params.Git_Branch} || true

                        docker run -p 5000:5000 \
                            --name "${APP_NAME}-${params.Git_Branch}" \
                            -d ${APP_NAME}:${BUILD_NUMBER}

                        docker ps
                    """
                }
            }
        }
    }
}
